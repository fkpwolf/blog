---
title: Web Service/SOA总结
author: Fan Fan
layout: post
permalink: /2010/03/23/web-service-summary/
aktt_notify_twitter:
  - no
dsq_thread_id:
  - 
categories:
  - 找工作
---
最近的一个项目大量用到了web service，这里作下总结。

主观的说，不大喜欢web service， 这个东西是很好，但是太复杂了。*More Joel on software* 里面提到了类似的落后技术战胜“先进”技术的场景，比如虽然HTML有这样那样的局限，但是依然很流行，因为它简单。

web service好在哪里呢？标准和规范。所有的报文格式都有明确而细致的定义，方法也有定义，有点偏向“基于契约编程”和“接口编程”。这些适合企业内部的信息集成，适合变化缓慢的业务需求。而对web应用（互联网行业）这种快速开发模式来说，web service就显得笨重和过度设计。

所以如果使用了web service，必定要多多应用[SOA的理念][1]，“&#8230;把业务转换为一组相互链接的服务或可重复业务任务”，这样web service优点才能体现出来。

做多了企业信息系统后，发现这个东西很boring，官僚，保守&#8230; 啥都利益导向，没劲。

 [1]: http://www.ibm.com/developerworks/cn/webservices/newto/