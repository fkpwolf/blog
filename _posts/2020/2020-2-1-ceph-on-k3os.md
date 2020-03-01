---
layout: post
title: "Ceph on K3OS - 一种快速部署 Ceph 的方法"
date: 2020-2-1
categories:
  - cloud
  - devops
typora-root-url: ../../../blog
---
### 目的
创建一个高性能的 Ceph 集群，来为另外一个 k8s 集群提供快存储服务。

### 使用 k3os 部署 Kubernetes
一个 master node + 三个 work node，每个 work node 连接一个 NVME 磁盘。

```sh
virt-install \
  --import --disk /var/lib/libvirt/images/test1.qcow2,bus=virtio \
  --name k3os \
  --ram 2000 \
  --vcpus 2 \
  --disk /var/lib/libvirt/images/k3os-amd64.iso,device=cdrom \
  --boot kernel=/var/lib/libvirt/images/k3os-vmlinuz-amd64,initrd=/var/lib/libvirt/images/k3os-initrd-amd64,kernel_args="console=tty1 console=ttyS0 k3os.fallback_mode=install \
k3os.install.silent=true k3os.install.tty=ttyS0 k3os.install.device=/dev/vda \
k3os.install.config_url=https://temp.blob.core.windows.net/file/config.yaml \
k3os.password=rancher" \
  --console=pty,target_type=serial \
  --os-type linux \
  --os-variant generic \
  --host-device=pci_0000_09_10_0 \
  --nonetworks \
  --graphics none
```
上面的 config.yaml 文件内容：
```yaml
ssh_authorized_keys:
- "ssh-rsa AAAAB3NzaC1y... fan@fans-iMac.lan"
- "github:fkpwolf"

k3os:
  k3s_args:
  - server
  - "--disable-agent"
```

该命令可以：
1. 创建的虚机启动后会自动安装 k3os，不需要手工干预
2. config.yaml 主要保存登录公钥
3. host-device 用来做 pci 穿透，这里赋予了两个 pci 设备：网卡和 NVME 卡

使用这种命令行的方式比用图形化的 virt-manager 优势在于：
1. 可以使用 tty 串口直接访问控制台，可能需要和上面一样传递内核参数
2. 传递参数更为快捷

### 部署 Rook Ceph

配置`hostNetwork: true`，这样外部（另外一个 k8s 集群）就可以直接通过外部 IP 来访问 Ceph MON，否则默认是 k8s 内部 ClusterIP。设置好后，`kubectl get pods -n rook-ceph -o wide` 会看到外部 IP 地址。

### 使用


### Think
如果把 Ceph 集群和 k8s 集群合在一个大集群里面，资源利用率会比这种分离模式更高，这里只是测试的目的。在现在 k8s 弱多租户的情况下，分离也许是种常见场景。