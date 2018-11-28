---
layout: post
title:  "Art of Kubernetes"
date:   2018-10-11 14:20:33
---

1. 声明式接口，比如 job，不需要实现某个语言接口（非侵入式），全部 yaml 和参数。对比 OSGI 简洁多。这个也是 CRD 来源。
2. create 后能 delete，对于长时间运行平台，这个很重要
3. 无处不在的分离（隔离）
4. 滚动升级 After new RC created, old RC then be deleted
5. 异步任务，比如删除 namespace，涉及 pods, pv这些外部资源，虽耗时很长，但能保证完成。这个时候如果操作这个 namespace，资源有个 Terminating phrase.
6. 创建任务时会反复 repeat（重试），这个机制有利于不稳定的分布式环境。liveness & probeness 保证 pod 正常运行。这都提高了可用性。
7. 提供了分布式系统的快速开发、测试、运行、监控的环境，考虑到了方方面面。其对对等架构很容器部署，对于 master-slave 或者有状态的则显笨拙，以至于必须用复杂的 operator，这是正途么？
8. 各种 chart 的组合能帮助我们快速实现原型，确实做到了 Infrastructure as Code.
9. CLI 精心设计，功能强大。kubectl proxy & kubectl port-forward，方便外部访问，分布式环境下很实用，方便测试、调试和开发。