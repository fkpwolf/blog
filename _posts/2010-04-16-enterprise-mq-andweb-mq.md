---
title: 企业应用的MQ和互联网的MQ
author: Fan Fan
layout: post
permalink: /2010/04/16/enterprise-mq-andweb-mq/
aktt_notify_twitter:
  - no
dsq_thread_id:
  - 650969551
categories:
  - methodology
---
一般说，消息队列是改进系统伸缩性的选择。一则消息是异步的，二则这会降低系统的耦合度。东西是好东西，有人说jms或者mdb是j2ee规范中最好的标准，不过最近项目中用到的JMS感觉却很痛苦。配置很痛苦，调用很痛苦，调试也很痛苦。感觉这东西是上古的东西，在java中使用很麻烦。

最近看到[twitter的架构][1]居然也用MQ，很感兴趣。看了下，跟IBM MQ Server完全不一样，笑。这个原来是基于Memcached协议的一个list，放在内存中，主要强调性能，也可以cluster。调用么，rest style，get/set，标准的东西，对客户端没啥要求，不需要额外包，更不需要预先配置，一个URL搞定。看看[这里][2]，觉得也不复杂。

twitter很多东西都是[开源][3]了，活雷锋。MQ的这个东西在[这里][4]，称为kestrel。基于jdk6 + scala，actor的并发模式，有人已经[走读][5]过了。Cool!

相对企业MQ，这东西局限在：1）消息难做到严格的排序；2）事务。大多数情况下不是致命。

多数情况下，如果一个技术上手容易，虽然功能少点，但是也会尝试的在系统中使用。如果跟IBM MQ Server一样的巨无霸，碰的人不多，推广开也难了。

 [1]: http://www.infoq.com/cn/news/2009/06/Twitter-Architecture
 [2]: http://btmao.org/2009/09/22/cacheandcache_1/
 [3]: http://blog.twitter.com/2009/01/building-on-open-source.html
 [4]: http://github.com/robey/kestrel
 [5]: http://dingsding.com/?p=95