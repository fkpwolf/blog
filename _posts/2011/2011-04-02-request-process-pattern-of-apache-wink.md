---
title: 'Apache Wink代码阅读 &#8211; 请求处理模式'
author: Fan Fan
layout: post
permalink: /2011/04/02/request-process-pattern-of-apache-wink/
dsq_thread_id:
  - 617445293
categories:
  - arch
---
Apache Wink是个实现REST服务的框架，本身基于Servlet，逻辑比较简单。其核心为HTTP Request的 一个处理过程：1）RestServlet收到request；2）使用Bean Factory创建一个Resource Bean；3）给bean赋值；4）调用Bean相应方法；5）返回response。

这里面跟一般的Servlet应用区别的是使用Resouce Bean作为Controller，对应Struts中的Action Bean。不同的是REST使用了annotation来注入上下文，下面的一个Resource Bean就注入了三个属性，而Struts直接用参数来传递request, response信息。（使用annotation有个附带的好处：非常方便单元测试）

<pre class="brush:java">@Path("/person")
public class PersonResource{
  @Context
  UriInfo uriInfo;
  @Context
  HttpServletResponse resp;
  @Context
  HttpServletRequest req;

  @GET
  @Produces(MediaType.APPLICATION_JSON)
  public JSONObject getPerson(@QueryParam("id") String id)  {
    String path = req.getPathInfo();
    ......
  }
}</pre>

所以Resouce Bean是有状态的么？如此一来岂不比单实例多线程的Servlet模式效率要差？难道跟Spring一样使用ThreadLocal？（后来证明这种主动的思考模式很有效，赞！）

查找代码，发现类RuntimeContextTLS（org.apache.wink.common.internal.runtime）将每个request处理线程（Servlet thread）需要的RuntimeContext保存在ThreadLocal成员变量中。类CreationUtils( org.apache.wink.common.internal.lifecycle)的方法createObject创建Resource实例并且inject属性（放在RuntimeContext中)。

ThreadLocal的特征使多线程的处理模式中不必一直携带Context，也就是即使Resource Bean只有一个对象实例，在多个线程调用其方法时，也不会出现对http request数据访问的同步问题，而annotation inject也使Resource Bean的方法定义中不必携带Context（因为是类的成员变量），这个跟Spring处理JDBC connection一样。

Context好处在于调用方法中不必传递大量参数，坏处在于过多的状态使方法行为难以确定，这就走到了Functional Langue的势力范围。

Wink可以与常用DI框架比如Spring, Guice集成起来。比如在wink-spring-support这个子project中，类SpringLifecycleManager（org.apache.wink.spring.internal）使用Spring本身的object factory来管理Resource Bean，然后类DependenciesInjectionPostProcessor会使用上面提到的CreationUtils来注入context。

上面的分析没有考虑Wink有自己扩展的annotation：Scope，它模仿了Spring，分singleton和prototype两种（参见Wink的user guide）。