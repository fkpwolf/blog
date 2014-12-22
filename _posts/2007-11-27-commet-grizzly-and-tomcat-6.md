---
title: Commet, Grizzly and Tomcat 6
author: Fan Fan
layout: post
permalink: /2007/11/27/commet-grizzly-and-tomcat-6/
blogger_blog:
  - fkpwolf.blogspot.com
blogger_author:
  - fannoreply@blogger.com
blogger_permalink:
  - /2007/07/commet-grizzly-and-tomcat-6.html
dsq_thread_id:
  - 643381227
categories:
  - Web Development
---
<span style="font-size: 100%">&nbsp; 一个比较早的对这方面讨论的帖子在这里: <a href="http://macrochen.javaeye.com/blog/28020">Comet，下一代Ajax？</a> DW上面好几篇文章做了介绍：<a href="http://www.ibm.com/developerworks/cn/web/wa-lo-comet/index.html">Comet：基于 HTTP 长连接的“服务器推”技术</a>，<a href="http://www.ibm.com/developerworks/cn/java/j-jettydwr/">面向 Java 开发人员的 Ajax: 使用 Jetty 和 Direct Web Remoting 编写可扩展的 Comet 应用程序</a>。在《Ajax Design Patterns》这本书上也有介绍，不过称为HTTP Streaming。<a href="http://www.ibm.com/developerworks/cn/web/wa-aj-web2jee/index.html">Java EE 迎合 Web 2.0 采用事件驱动的异步架构应对现代 Web 应用程序带来的挑战</a>这篇文章从理论的高度说明了异步模式对提高系统吞吐量的作用。<br />如果要实现Comet，Server端就需要NIO来解决HTTP长连接的问题，否则Server端支持的并发数就会大大减少。</span>

<span style="font-size: 100%">&nbsp; Servlet和NIO的使用可以参考<a href="http://www.ibm.com/developerworks/cn/java/j-nioserver/index.html">Servlet API 和 NIO: 最终组合在一起 使用非阻塞 I/O 构建基于 Servlet 的 Web 服务器</a>。虽然是一篇较早的文章，仍然提供了很多关于NIO的有用信息。</span>

<span style="font-size: 100%">&nbsp; Tomcat 6里面相关NIO的配置可以参考其<a href="http://tomcat.apache.org/tomcat-6.0-doc/config/http.html">文档</a>，只用修改Connector的protocol即可。接下来可以写一个简单的<a href="http://tomcat.apache.org/tomcat-6.0-doc/aio.html">Servlet</a> A，其implements CometProcessor接口。为测试方便，将Sender存放在Application变量中，在另外一个Servlet B中就可以调用Sender的send(&#8230;)方法，当浏览器访问A时，可以动态看到Server端push过来的信息（这时浏览器会一直处于页面正在装载的状态，实际应用中可以用AJAX来接受信息）。</span>

<span style="font-size: 100%">&nbsp; 由于是长连接，数据在传送过程中就存在缓冲的问题。一般的一个Server端的发送到客户端的消息（注意这里不是一个响应）不会很大，而默认的Tomcat<a href="http://tomcat.apache.org/tomcat-6.0-doc/aio.html">缓冲配置</a>偏大，导致客户端响应滞后，出现这种奇怪的现象：1）当send(&#8230;)方法触发多次后，server才会一次把消息全部push到客户端（即是每次使用了flush方法）；2）1 发生后，每次的send(&#8230;)的调用会导致server段立即把这条消息发送到客户端（这是buffer又好像不存在一样）。</span>

<span style="font-size: 100%">&nbsp; 对于Tomcat 6自带例子里面的bug，也有位哥们在他的blog中提到：<a href="http://blogs.webtide.com:80/gregw/2007/11/22/1195699800000.html">Asynchronous IO is hard!</a>。</p> 

<p>
  &nbsp;
</p>

<p>
  </span>
</p>