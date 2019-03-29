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

既然是轻量级，当然得跑在 ARM 上才有存在感。Odroid HC1/XU4 虽然有点老，也不是 arm64 的，胜在软件稳定，CPU 性能也比一般的 A53x4 arm64 设备比如 rk3328 要强些，当然有更强的比如 rk3399 + eMMC，没银子置办，而且我的也可以接大容量磁盘，聊以慰藉。跑容器应用，一个镜像动辄 1GB，系统盘还是大点好，32GB 比较合适。

现在的 k3s 已经是 all-in-one 了，包含了 kubectl+containerd+runc+flannel，部署似乎比 kubeadm 还要方便很多。

### Inside

除了做减法（去掉不必要组件），k3s 也做了些加法，比如 helm chart 的自动安装和内置 LoadBalancer 功能。

pkg/helm/controller.go watch CRD 然后创建klipper-helm pod，在pkg/server/server.go里面注册，hard code，里面还有个pkg/servicelb/controller.go，自动生成 load balance pod 的。其他的官方组件是在哪里注册并且运行的呢？官方组件不需要注册，pkg/daemons/control/server.go 会启动所有组件比如 api server, scheduler, controller，这个文件最后还是由 pkg/server/server.go 调用。

如果 service 为 LoadBalancer，会自动生成一个svclb开头的 pod，rancher/klipper-lb，这样 lb service 就可以获取 ip 了，在没有云环境情况下，这个解决了遗留已久的问题，比如我把 dashboard service 改为 lb马上就可以获取 IP 了，颇为神奇。<https://github.com/rancher/klipper-lb> 不知道是否要对 k8s 核心进行修改。