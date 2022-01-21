---
layout: post
title: "手动在 KVM 上面使用 cloud-init image"
date: 2020-5-6
categories:
  - cloud
typora-root-url: ../../../blog
---
### Image list
- Ubuntu <https://cloud-images.ubuntu.com/releases/16.04/release/> .img是给QEUM和KVM用，.vhd是给Azure用，vmdk是给Vmware用。一般这些是在 OpenStack 这样的 IaaS 平台上面使用。这里描述如何在安装有 KVM 的普通 Linux 机器上面使用这些 Image。
- Fedora <https://cloud.fedoraproject.org/> Fedora cloud image，比 Ubuntu 兼容性好。
- OpenSuse <https://get.opensuse.org/leap/> Jeos is for cloud. Leap is normal version, Tumbleweed is rolling update. openSUSE MicroOS is a variant of openSUSE Tumbleweed and serves as a base of openSUSE Kubic, a Container as a Service platform. Guide of kubeadm’s installation container runtimes uses Tumbleweed. `KVM and XEN` version of JeOS image is cool: need interactive installation and it is fast. Doesn’t need extra disk for OS so boot image is enough. `OpenStack-Cloud` filesystem is xfs and 1G size. `KVM and XEN` filesystem is btrfs and 26G size. But Tumbleweed `KVM` version didn't have igbvf driver. Crazy! Need `zypper install kernel-default`. It is 5.11.11. So what is stock or default kernel version?
- Centos Stream <https://cloud.centos.org/centos/>
- Rocky <https://download.rockylinux.org/pub/rocky/8.4/images/>
- Linux Amazon Linux 2 https://aws.amazon.com/cn/amazon-linux-2/faqs/

### Create VM
* <https://people.redhat.com/mskinner/rhug/q3.2014/cloud-init.pdf>
* cloud-init 工作原理 - 每天5分钟玩转 OpenStack [IBM DW](https://www.ibm.com/developerworks/community/blogs/132cfa78-44b0-4376-85d0-d3096cd30d3f/entry/cloud_init_%E5%8E%9F%E7%90%86_%E6%AF%8F%E5%A4%A95%E5%88%86%E9%92%9F%E7%8E%A9%E8%BD%AC_OpenStack_171?lang=en).
* 手工要专门的办法做cloud-init <http://ubuntu-smoser.blogspot.com/2013/02/usooing-ubuntu-cloud-images-without-cloud.html> 如何把用户信息注入到里面

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
## Convert the compressed qcow file downloaded to a uncompressed qcow2 
$ qemu-img convert -O qcow2 download-disk.img disk.qcow2 
## create the disk with NoCloud data on it. 
$ cloud-localds my-seed.img my-user-data 
If not the command, run: genisoimage  -output seed.iso -volid cidata -joliet -rock user-data meta-data 
## Create a delta disk to keep our .orig file pristine 
$ qemu-img create -f qcow2 -b disk.qcow2 disk-new.img 
## Boot a kvm 
$ kvm -net nic -net user -hda disk.img -hdb my-seed.img -m 512
```
密码似乎不能用 123123 这种简单的。完整的 user-data spec 可以参考 http://cloudinit.readthedocs.io/en/latest/topics/examples.html，更多操作指南参考 KVM / libvirt。

对于 Centos Steam，上面配置无法 login，[这里](https://github.com/cockpit-project/bots/tree/main/machine) 的 cloud-init.iso 可以用。原因未知。

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

为什么每次启动 vm 后其virtual function mac地址会变呢？算了，最后我还是自己修改 os 配置为静态 ip 地址。对于 Centos，要删除 /etc/sysconfig/network-scripts/ifcfg-ens3 文件里面的 HWADDR=xxx 和 BOOTPROTO=dhcp。

### Resize image
Ubuntu cloud image default size is 2GB, you can use ‘qemu-img info a.img’ to check details. To resize it:

1. You need to convert the qcow2 image to raw
qemu-img convert -O raw guest.img guest.raw
2. Then resize the raw file
qemu-img resize guest.raw 10G
3. Then convert it back to qcow2(this will create 1G file, 1G = 10G * 0.10)
qemu-img convert -O qcow2 -o compat=0.10 guest.raw guest.img.10g
4. Then run the guest and resize your file system

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
`virt-install --import --disk /var/lib/libvirt/images/focal-server-cloudimg-amd64-disk-kvm.img,bus=virtio --name vm1--ram 2000 --nographics`

https://quantum-integration.org/posts/install-cloud-guest-with-virt-install-and-cloud-init-configuration.html
--nographics option forces virt-install to redirect the console output to the terminal window. After successful boot you get to the vm promt:

For Debian, it will start an install dialog.

`virt console vm`
to connect running vm.
The default escape key - to exit: ^[ ( Ctrl + [ )

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
