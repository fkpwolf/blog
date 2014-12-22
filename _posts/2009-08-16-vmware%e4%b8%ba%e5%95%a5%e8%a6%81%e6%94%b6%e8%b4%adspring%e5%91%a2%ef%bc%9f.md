---
title: VMware为啥要收购Spring呢？
author: Fan Fan
layout: post
permalink: /2009/08/16/vmware%e4%b8%ba%e5%95%a5%e8%a6%81%e6%94%b6%e8%b4%adspring%e5%91%a2%ef%bc%9f/
categories:
  - Cloud Computing
---
居然以<a href="http://www.javaeye.com/news/9682-vmware-acquire-spring-to-paas" target="_blank">4.2亿</a>的价格收购了这个开源框架！让人羡慕的同时不免疑惑，VMware是搞虚拟机的，为什么要收购一个java做open source公司呢？

这可以看作VMware在自身虚拟机市场上的一个拓展和加深，目的是提供更丰富feature的云计算平台。虚拟机可以看成云计算的基础层面，而spring则可以看作应用层。使用spring的<span style="color: #ff0000;"><strong>IOC模式</strong></span>把虚拟机提供的服务比如登录、存储提供给PAAS开发者，大概可以吸引不少眼球，因为很多的java开发者都比较熟悉和推崇spring。

GAE（google app engine）如何做的呢？大多地方google都是简单的使用了Factory模式来得到预设的服务。比如在用户验证这里：

<pre class="brush:java">public class MyServlet extends HttpServlet {
    public void doGet(HttpServletRequest request, HttpServletResponse response)
            throws IOException {
        UserService userService = UserServiceFactory.getUserService();

        String thisURL = request.getRequestURI();
        if (request.getUserPrincipal() != null) {
            response.getWriter().println("Hello, " +
                                         request.getUserPrincipal().getName() +
                                         "!  You can <a href="\">sign out</a>.");
        } else {
            response.getWriter().println("Please <a href="\">sign in</a>.");
        }
    }
}</pre>

云计算平台除了提供硬件外，还要提供marsh-up的工具，将各个层粘合起来，这样才算是个平台。比如GAE的python平台就提供了<a href="http://code.google.com/intl/zh-CN/appengine/docs/python/gettingstarted/usingwebapp.html" target="_blank">webapp框架</a>的接口，开发者可是使用Django，这种做法就比较友好。