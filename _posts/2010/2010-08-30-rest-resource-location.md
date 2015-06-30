---
title: REST的资源定位
author: Fan Fan
layout: post
permalink: /2010/08/30/rest-resource-location/
aktt_notify_twitter:
  - no
  - no
dsq_thread_id:
  - 654323790
categories:
  - arch
tags:
  - rest
---
Story是这样的：客户查找书籍，server返回一堆书，每本书包含一个书的详细信息的href。假设我有“书的详细信息”这样一个resource：

@GET @Path(&#8220;/book/{library}/{bookId}&#8221;)  
public Response getBookInfo(&#8230;)

这样在server端，我们就需要build这样一个URI。通常做法是hard coding，REST(这里是JAX-RS)则使用[这样的方法][1]:

UriBuilder ub = uriInfo.getBaseUriBuilder().path(BookResource.class).path(BookResource.class, &#8220;getBookInfo&#8221;);  
URI uri = ub.build(aLibrary, aBookId);

第一行代码中的*getBookInfo*是方法名。

个人觉得这种方式有些新意。当年Struts就是靠灵活的跳转机制流行一时，REST则充分利用了anotation，避免使用复杂的配置文件，而且代码上也比hard coding的URL更有维护性和可读性。当然，如果*getBookInfo*不用字符串则更能体现静态语言的优点了。

那么，为什么“资源的定位”对于REST很重要呢？

对于一个Web应用，URI是很重要的一部分，这包括他的schema定义、URI的build以及路由，涉及到web开发的方方面面。REST里面有个[概念][2]：*hypermedia as the engine of application state*。以上面的例子为例，hypermedia指的是server端返回的一个指向书详细信息的link，这个link是在server端construct的，client端拿到这个link后，就知道下一步跳转的方向，这样flow运行下去。整个应用就如同一状态机一样，从一个状态跳到另外一个状态。如果拿到的比如是孤立的信息，比如aLibery和aBookId，这时候客户端就要知道如何build link，而这一般都不是很容易的事情，也增加了系统的耦合度。而server端build href后，URI schema变化的代价就更小。这样我们就不必拘泥于web 2.0所说的：一个href应该永远可以访问。

 [1]: https://jersey.dev.java.net/nonav/documentation/latest/user-guide.html#d4e389
 [2]: http://barelyenough.org/blog/2007/05/hypermedia-as-the-engine-of-application-state/