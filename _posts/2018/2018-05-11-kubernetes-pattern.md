---
layout: post
title:  "Kubernetes 开发模式"
date:   2018-05-11 14:20:33
typora-root-url: ../../../blog
---

这里描述使用 Kubernetes 来开发分布式应用可能会用到的模式，并非其内部实现模式。

### Sidecar（边车、挎斗）
最常用模式，指在一个 Pod 中同时运行两个容器，容器共享网络和文件系统。这个也是 Pod 对容器本身的一种扩展和抽象。
乍一看：这个很稀松平常，就和我们本地计算机运行同时运行多个程序并无二致，但可以成为最为广泛的模式了。
考虑到容器是一种程序的封装，这种 sidecard 模式就是封装的一种退化，也是容器之间最简单的交互方式。
如果 Kubernetes 更多的是强调模块的隔离和解耦，这个 sidecar 其实就是内聚的折中。

[Java Web Application with Tomcat and Sidecar Container](
https://github.com/kubernetes/examples/blob/master/staging/javaweb-tomcat-sidecar/README.md)

![](/images/2018/tomcat-sidecar.png)

把两个关系紧密的 container 放到一个 pod 里面，共享同样的文件系统。带来的好处是 Tomcat 和 sample 可以分开演化，image builder 的速度也加快了。k8s 的 "atomic” scheduling 保证了两个 container 是同时起来的，这样 Tomcat 总能访问到 war 文件，避免出现一前一后的尴尬问题。

### Ambassador

[Building Ambassador, an Open Source API Gateway on Kubernetes and Envoy](https://blog.getambassador.io/building-ambassador-an-open-source-api-gateway-on-kubernetes-and-envoy-ed01ed520844)

![](/images/2018/ambassador-pattern.png)

[Ambassador和Istio：边缘代理和服务网格](https://zhuanlan.zhihu.com/p/33205773) Ambassador 是一个 Kubernetes 原生的微服务 API 网关，它部署在网络边缘，将传入网络的流量路由到相应的内部服务（也被称为“南北”流量）。Istio 是微服务的服务网格，旨在将L7的可观察性、路由和弹性加入到从服务到服务的流量中（也被称为“东西”流量）。Istio 和 Ambassador底层都使用了 Envoy。 

### Shard

Hands On: Implementing a Sharded Redis 的例子
Redis 存储分片之代理服务Twemproxy 测试 http://blog.jiguang.cn/redis-twemproxy-benchmark/

![](/images/2018/shard-pattern.png)

前端使用 Twemproxy 做代理，后端的 Redis 数据能基本上根据 key 来进行比较均衡的分布。 这个功能对于大数据比如 Ceph 来说，都是内置的。这个 proxy 必须知道 key 会再在哪台服务器上面。而使用 StatefullSet 的原因是这样能保证 redis 的 pod host name 不会变化，proxy 有 key-hostname 的映射，所以 hostname 不能变化。k8s service 虽然也不会变，但是这里是 twemproxy 直接连 pod，中间不用 service 做负载均衡，service 的目的只不过是用来形成 sharded-redis-0.redis 这种一致的 DNS。

### Think

相比较经典的设计模式，这里的模式粒度更大（分布式），隔离性更好。通过网络的强制分离比原来的库、模块的隔离更为彻底，也带来了很多比如 log，monitor 的额外收益。通过网络的分离，也符合所有服务皆为微服务的宗旨。 