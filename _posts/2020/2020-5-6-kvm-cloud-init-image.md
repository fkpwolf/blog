---
layout: post
title: "Manually create KVM machine by cloud-init image"
date: 2020-5-6
categories:
  - cloud
typora-root-url: ../../../blog
---
### Image list
- Ubuntu <https://cloud-images.ubuntu.com/>
- Debian <https://cloud.debian.org/images/cloud/>
- Fedora <https://cloud.fedoraproject.org/> 
- OpenSuse <https://get.opensuse.org/leap/> Jeos is for cloud. Leap is normal version, Tumbleweed is rolling update. openSUSE MicroOS is a variant of openSUSE Tumbleweed and serves as a base of openSUSE Kubic, a Container as a Service platform. Guide of kubeadm’s installation container runtimes uses Tumbleweed. `KVM and XEN` version of JeOS image is cool: need interactive installation and it is fast. Doesn’t need extra disk for OS so boot image is enough. `OpenStack-Cloud` filesystem is xfs and 1G size. `KVM and XEN` filesystem is btrfs and 26G size. But Tumbleweed `KVM` version didn't have igbvf driver. Crazy! Need `zypper install kernel-default`.
- Centos Stream <https://cloud.centos.org/centos/>
- Rocky <https://download.rockylinux.org/pub/rocky/8.4/images/>
- Linux Amazon Linux 2 <https://aws.amazon.com/cn/amazon-linux-2/faqs/>

### Create VM
```sh
## Install a necessary packages 
$ sudo apt-get install kvm cloud-utils genisoimage 
## URL to most recent cloud image of 12.04 
$ img_url="http://cloud-images.ubuntu.com/server/releases/12.04/release" 
$ img_url="${img_url}/ubuntu-12.04-server-cloudimg-amd64-disk1.img" 
## download the image 
$ wget $img_url -O disk.img.dist 
## Create a file with some user-data in it 
$ cat > my-user-data <<EOF 
#cloud-config 
password: passw0rd 
chpasswd: { expire: False } 
ssh_pwauth: True
ssh_authorized_keys: 
  - ssh-rsa AAAAB3NzaC1yc2EAAAADABUB... fan@fandeiMac.lan
EOF 
## convert the compressed qcow file downloaded to a uncompressed qcow2 
$ qemu-img convert -O qcow2 download-disk.img disk.qcow2 
## create the disk with NoCloud data on it. 
$ cloud-localds my-seed.img my-user-data 
## if command not exist, run: genisoimage  -output seed.iso -volid cidata -joliet -rock user-data meta-data 
## Create a new delta disk to keep our .orig file pristine 
$ qemu-img create -f qcow2 -b disk.qcow2 -F qcow2 disk-new.img 
## boot a kvm 
$ kvm -net nic -net user -hda disk.img -hdb my-seed.img -m 512
```
密码不能用 123123 这种简单的。完整的 user-data spec 可以参考 http://cloudinit.readthedocs.io/en/latest/topics/examples.html，更多操作指南参考 KVM / libvirt。

对于 Centos Steam 和 Kubic，上面配置无法 login，[这里](https://github.com/cockpit-project/bots/tree/main/machine) 的 cloud-init.iso 可以用，原因未知。

上面配置要是能设置 static IP 和 hostname 就好了，有点怀念 Vagrant。

### 网络
详细配置看 <http://cloudinit.readthedocs.io/en/latest/topics/network-config-format-v2.html>
<https://gist.github.com/smoser/635897f845f7cb56c0a7ac3018a4f476> 这个提供了样例和调试的方法。
其中match的方法对于我来说没啥用，因为mac地址是virtual function interface动态生成的，不固定。用match driver igbvf，发现：
Kernel driver name, corresponding to the DRIVER udev property. Globs are supported. Matching on driver is only supported with networkd.

找了一圈，只有Fedora用的是systemd networkd，其他用的都是/etc/network/interface 这种老式的方式。
但是其cloud-init log里面有错误：AttributeError: 'NoneType' object has no attribute 'iter_interfaces'
嗯，centos呢？也是一样的错误。发现 centos7 (kernel v3.10) vm 启动很快，比 ubuntu 16.04 快多了。[16.04 cloud image hangs at first boot](https://bugs.launchpad.net/cloud-images/+bug/1573095)，原来这个和 TTY有关系。但是我看 virt-manager 默认已经帮我生成好了啊。又尝试了下 Debian cloud image，也很快。
仔细查看上面 v2 文档，发现其需要 netplan，这样只有 Ubuntu 17.10 支持了？v1 我试过是可以的，但是其只能用 mac 地址匹配。

对于Ubuntu/Debian，手工修改 /etc/netplan/50-cloud-init.yaml 这个文件即可，不需要额外运行命令 netplan。

为什么每次启动 vm 后其virtual function mac地址会变呢？算了，最后我还是自己修改 os 配置为静态 ip 地址。对于 Centos，要删除 /etc/sysconfig/network-scripts/ifcfg-ens3 文件里面的 HWADDR=xxx 和 BOOTPROTO=dhcp。

手工配置IP为DHCP的方法：
1. Debian, edit `/etc/systemd/network/enp1s0.network` file

### Resize image
Ubuntu cloud image's default size is 2GB. You can use `qemu-img info a.img` to check details. To resize it:

1. Convert the qcow2 image to raw format
`qemu-img convert -O raw guest.img guest.raw`
2. Resize the raw file
`qemu-img resize guest.raw 10G`
3. Convert it back to qcow2(this will create 1G file, 1G = 10G * 0.10)
`qemu-img convert -O qcow2 -o compat=0.10 guest.raw guest.img.10g`
4. Power on vm and resize file system within the vm
    * run `parted /dev/vda` to edit the partition. set resize = "-1" (minus 1 means 1 sector from end of disk)
    * run `resize2fs /dev/vda1`

Using this way, ubuntu need lots of time to boot. It hang on “ systemd:started journal service”. I thought it was because I used virtIO disk or a small virtual image as disk. But at last it turn out
Centos use xfs filesystem, after reboot, disk was enlarged.

Check snapshot file details:
```
fan@ubuntu-gg:~$ qemu-img info vm2.img
image: vm2.img
file format: qcow2
virtual size: 10G (10737418240 bytes)
disk size: 196K
cluster_size: 65536
backing file: ubuntu-16.04-server-cloudimg-amd64-disk1.img.10g
Format specific information:
    compat: 1.1
    lazy refcounts: false
    refcount bits: 16
    corrupt: false
```
It is small now but increase fast and soon it will be same size as original file. 

### Useful commands
`virt-install --import --disk ubuntu1.img,bus=virtio --disk my-seed.img,bus=virtio,shareable=on --name vm1 --ram 8192 --nographics --osinfo ubuntujammy`

`--osinfo` value comes from output of `virt-install --osinfo list`. `--nographics` option forces virt-install to redirect the console output to the terminal window. After successful boot you get to the vm promt.
And you can still access the text console by Virt Manager. 

`virsh console vm`
to connect running vm.
The default escape key - to exit: ^[ ( Ctrl + ] )

[Installing a KVM Guest OS from the Command-line (virt-install)](https://www.techotopia.com/index.php/Installing_a_KVM_Guest_OS_from_the_Command-line_(virt-install)) command reference

[KVM Install from Console](https://arstechnica.com/civis/viewtopic.php?f=16&t=1165804)
```
virt-install -n nwtest --description Test -r 512 --vcpus 1 \ 
   --location http://ftp.ca.debian.org/debian/dists/squeeze/main/installer-amd64/ \ 
   --os-type=linux --os-variant=debiansqueeze\ 
   --disk /srv/virtual/nwtest,device=disk,bus=virtio,size=3 \ 
   --network bridge=br0,model=virtio \ 
   --autostart \ 
   --nographics \ 
   -x "console=tty0 console=ttyS0,115200n8" 
Directly install from scratch, step by step. the location parameter specific kernel and initrd address. It is net-install indeed.
```

`virsh domxml-to-native`
[this command ](https://unix.stackexchange.com/questions/587045/convert-libvirt-xml-into-qemu-command-line) can 
convert virt instance into a vm creation command.

```
genisoimage -o image.iso -r /path/to/dir
virsh attach-disk guest image.iso hdc --driver file --type cdrom --mode readonly
```
Use this way to transfer data to VM before network of the VM is ready.

If can't login VM caused by wrong configuration, mount VM image file by `guestmount` and then `chroot` into the mount point.
