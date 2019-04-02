---
layout: post
title: "k3s on Odroid HC1"
date: 2019-3-29
categories:
  - cloud
typora-root-url: ../../../blog
---
### Setup
『Lightweight Kubernetes. 5 less than k8s.』- 啊~五环，你比四环多一环~

![](/images/2019/k3s-odroid.JPG)

既然是轻量级，当然得跑在 ARM 上才有存在感。Odroid HC1/XU4 虽然有点老，也不是 arm64 的，胜在软件稳定，CPU 性能也比一般的 A53 x4 arm64 设备比如 rk3328 要强些，当然有更强的比如 rk3399 + eMMC，没银子置办，而且我的也可以接大容量磁盘，聊以慰藉。跑容器应用，一个镜像动辄 1GB，系统盘还是大点好，32GB 比较合适。

现在的 k3s 已经是 all-in-one 了，包含了 kubectl+containerd+runc+flannel，部署似乎比 kubeadm 还要方便很多。

### Inside

除了做减法（去掉不必要组件），k3s 也做了些加法，比如 helm chart 的自动安装和内置 LoadBalancer 功能。

pkg/helm/controller.go watch CRD 然后创建klipper-helm pod，在pkg/server/server.go里面注册，hard code，里面还有个pkg/servicelb/controller.go，自动生成 load balance pod 的。其他的官方组件是在哪里注册并且运行的呢？官方组件不需要注册，pkg/daemons/control/server.go 会启动所有组件比如 api server, scheduler, controller，这个文件最后还是由 pkg/server/server.go 调用。

如果 service 为 LoadBalancer，会自动生成一个svclb开头的 pod，rancher/klipper-lb，这样 lb service 就可以获取 ip 了，在没有云环境情况下，这个解决了遗留已久的问题，比如我把 dashboard service 改为 lb马上就可以获取 IP 了，颇为神奇。<https://github.com/rancher/klipper-lb> 不知道是否要对 k8s 核心进行修改。

使用了 sqlite 代替 etcd，所以现在还不支持 HA。这个能实现 watch 之类的接口？[Add support for SQL driver]( https://github.com/ibuildthecloud/k3s/commit/99d0d50bf8b8e97042f5f9241ab51cdfa9a42a13)，从这个 commit 看只要是 kv 数据库都可以，<https://github.com/ibuildthecloud/kvsql>，ibuildthecloud/kvsql/blob/master/clientv3/driver/generic.go 这个会把 kv 操作转换为 SQL 操作，而 watch/subscribe 似乎是在内存中操作，那重启后就没了啊，不可能吧，可能 watch list 也是一个 kv entry。

### 应用场景

[Kubernetes on the Edge](https://rancher.com/blog/2018/2018-12-11-kubernetes-on-the-edge/) Rancher 在国内的一个项目，奇葩之处是客户需要每个边缘节点上都要安装一个独立的 k8s 集群，因为网络不稳定，而且客户想用 k8s 方便部署升级的功能（多半类似电信光猫跑 OSGi Felix）。为了能让 k8s 运行在 ARM 4GB 内存节点上（好像一般便宜 ARM 板子都是这个规格😂 ），他们特意搞了个精简版 - <https://github.com/ibuildthecloud/k3s>，问起为什么有这个东东，[作者说](https://github.com/ibuildthecloud/k3s/issues/1)只是想学习下 k8s 的架构... 如果像 Linux Kernel 有个 `make menuconfig` 来模块化的编译 k8s 就好了，这样精简起来更容易，否则一个个 commit 来删除代码将来无法和 upstream 同步。

使用 arm 后，容器镜像是一个问题，因为一般只提供 amd64 架构的，直接运行会报 `exec format error` 的错。自己 build 也很麻烦，不想嵌入式直接 `gcc-arm-linux-gnueabihf` 就完事。<https://github.com/jessestuart/tiller-multiarch> 基于 multiarch/qemu-user-static 镜像编译，可以作为参考，跨平台很完善，`docker pull url` 里面也不需要加上处理器架构名称。build 文件在 .circleci 下面，可以跑在 CircleCI 上面。

### Think
* Go 在 arm 上编译很容易
* 这个项目热度现在很高，说明了虽然 k8s 相比 OpenStack 已经很轻量级，但是大家希望更轻量级
* k8s 的内部组件设计精妙、耦合度不高才容易简化，比如 etcd 用 sqlite 代替并不费劲
