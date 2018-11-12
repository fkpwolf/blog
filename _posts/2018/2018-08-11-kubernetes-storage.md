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

### Ceph BRD
大而全的终极方式

### Kubernetes 扩展功能
自动删除



<img src="/images/2018/pv.png">