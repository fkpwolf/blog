---
layout: post
title:  "我对 Kubernetes Operator 的理解"
date:   2018-11-01 14:20:33
---

### 为什么会需要 Operator？
Prometheus 是一个通用的监控工具，可以抓取任何符合格式的日志然后进行后续处理。他虽然是 CNCF 的项目，但是独立性很强，并没有对 Kubernetes 做特定的优化。而我们如果要在 Kubernetes 里面使用监控，除了一些额外的配置，比如监控所有节点，这个并不复杂，但是对于监控更多资源比如 Service，则需要额外的代码开发。这个开发是由 Kubernetes 端完成，因为有很多 k8s 细节的绑定，放到 Prometheus 里面肯定不合适。这个东西独立出来，就叫 Operator。
对于一般的用户，简单的配置还能自己处理，复杂的则需要专业知识。有了 Operator，他就能用 kubectl edit/delete 这种熟悉的 CRUD 方式来操作外部资源，内部资源有 pod，外部资源有告警规则，这就有了一致的视角。

### 运作模式

### 开发 Operator 的几种方式
Operator SDK
Kubebuilder
Metacontroller

### Think
如果套用 MVC 模式，这里 model 就是 CRD，C 当然是 operator，是 V 是什么呢？系统中运行的 pod？V 就是集群现在运行时，运行的快照（snapshot）。同步的机制就是 etcd 这个观察者模式。而且这些 View 也有自己的状态变化，也要和 model 里面的同步。如果我们采用 Redux 形式，一切皆从数据开始，改变 view 只能通过 event，但是 pod 是有自我运行时，能自我状态变化比如 pod 挂掉，这个和页面元素差别有点大。

Operator 代表的是领域知识，这个领域既包含特定集群，也包含 k8s。所以 operator 代码和 k8s 紧耦合是可以的，而这一切对于特定集群应该是透明的，集群应该不知晓其运行环境，也不操作 CRD。

这种通过 CRD 来扩展，来隔离前端和后端（前端只用对资源进行 CRUD），比 command-query 似乎更先进了一步。更重要，这个才是 k8s 声明式编程风格的根基。

Operator 实际是命令式的，是对声明式的一种补充，或者一个 patch，并不优雅。所有 heml 安装不足之处，应该由 k8s 本身来支持。比如第一个 node 挂了，后面如何加入。共同的模式应该提取出来。对于命令式的，就有必要加入一个容易操作的 UI 了。