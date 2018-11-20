---
layout: post
title:  "Kubernetes 存储概述"
date:   2018-08-11 14:20:33
---

有状态。这里是指每个 pod 的运行状态，如果每次都是一样的，则为无状态。

PV, PVC
Claim 表示申请，PV 则表示申请的结果。Volume 代表的是卷，对于容器来说，是一个已经格式化好已经挂载好的可读写目录。
所以 Kubernetes 在这里把存储做了一层抽象，对于跑在容器里面的代码来说是透明的：不用关心文件系统格式，直接用就好了。

### NFS
这个可能是最简单的方式

### Gluster
红帽的方案，已经很成熟了，在 Kubernetes 里面支持的也比较好。结合 Heketi 来控制，因为是对等架构，一台也可以运行，不像 Ceph 至少得有三台节点而且还分 osd 节点 monitor 节点，所以安装就容易多了。

### Ceph BRD
大而全的终极方式，安装麻烦。但是大家用的比较多，所以也很常见。要在 k8s 里面用起来坑极多，一个 StorageClass 就有各种版本。后来换成 Rook，简单省心，直接通过 Operator 管理，这才是 k8s 的适用场合啊！

### Cinder
如果在 OpenStack 里面搭建 k8s 集群，这个更容易，都帮你弄好了。Cinder 是 OpenStack 的块存储统一访问接口（是的，目的和这里的 k8s 一样），后端实现一般也是 Ceph。

### Kubernetes 扩展功能
自动删除


![pv](/images/2018/pv.png)