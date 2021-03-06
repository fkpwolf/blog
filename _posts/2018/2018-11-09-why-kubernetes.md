---
layout: post
title:  "为什么要使用 Kubernetes？"
date:   2018-11-09 14:20:33
---
对于一般开发人员来说，不用了解 Kubernetes 内部机制，为什么要使用 Kubernetes 呢？

1. 微服务 <https://martinfowler.com/articles/microservice-trade-offs.html> Strong Module Boundaries, Independent Deployment, Technology Diversity，这三者都能满足，这其实是容器带来的好处。隔离：存储、网络、访问 Ingress。代价：Distribution, Eventual Consistency, Operational Complexity。

2. DevOps 微服务切分后，盒子也打开了。首先是开发部署都很麻烦，有了 Kubernetes + CICD，这个流程才能顺畅的跑起来。

3. 监控 包括日志、跟踪、遥测，对于采用微服务架构都这些都是必须的。这些在一般微服务架构中都会遇到，也有对应的方案。那 Kubernetes 能带来哪些额外的便利呢？更一致的划分带来更多的隔离，也带来更多监控点（或者切面）。比如 Ingress/sidecar、比如 network policy。

4. 快速上手和便于管理 PaaS 平台的好处也就是这些了，一般是对运维可能好处更多，但是现在 Infractaure as Code，有了 Operator，开发人员也可以快速的创建和管理集群。

这是 why，对于 how - 如何有效利用，见[一般开发者的 Kubernetes 使用指南]({{ site.baseurl }}{% post_url 2018/2018-09-01-kuberntes-for-dev %})

对于一个开发者，何为一个好的容器平台呢？一个好的云平台应该是什么样的呢？

1. 隔离
对于开发者来说，本地开发调试成功，部署后也应该运行成功。

2. 稳定
如果基础服务不稳定，开发者的代码在不断迭代中出现问题必然会抓狂。

3. 诚实
方便管理但是并不掩藏细节，不越俎代庖。开放。开发者不是小白。
