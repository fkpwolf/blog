---
title: 从JSP到Velocity
author: Fan Fan
layout: post
permalink: /2008/03/15/%e4%bb%8ejsp%e5%88%b0velocity/
blogger_blog:
  - fkpwolf.blogspot.com
blogger_author:
  - fannoreply@blogger.com
blogger_permalink:
  - /2008/03/jspvelocity.html
dsq_thread_id:
  - 682166860
categories:
  - Web Development
---
1.JSP有优点。  
Rails页面脚本的做法跟JSP其实差不多，也是在HTML中通过<%...%>将Server端语言嵌入其中，这应该是最容易实现的方法。如果说比JSP有强出的地方，那就是Ruby的语法比Java简洁很多。  
Grails:  
<g:each in="${tripList}" status="i" var="trip"></g:each>  
Struts:  
<logic:notpresent parameter="queryId"></logic:notpresent>  
对于这种用XML格式来表达逻辑，同JSTL大同小异，个人感觉总是别扭。一则不如Server端语言灵活，二则要掌握另外一种语法。  
相比velocity就做的很直观了：  
`<br />#foreach ( $item in [1..5] )<br />On this iteration, refers to the value $item.<br />#end<br />`  
其做法更多是贴近一般语言的习惯，仿佛只不过是在语句前加上#（velocity里面称为Directives），变量前加上$。  
Grails里面变量的表示为&#8221;${}&#8221;。一些流行的Javascript框架如prototype、JQuery也采用这种写法。不过这种变量的表示在JSP 2.0里面也被引入，我们就再也不必看到难看如<%=name%>的写法了。

对于XML格式的标签写法，可能对于机器来说容易处理，其实在这里没有一般的语法的校验，有的只是XML的scheme的校验，当然这也是一种语法，不过就容易处理多了。  
对于Velocity，其采用了Antlr来产生语法分析器，在其源代码包org\apache\velocity\runtime\parser 下可以看到详细的代码。

还有另外一种模板框架freemarker，跟velocity很类似。

如果我们采用更为简洁的做法：将逻辑和页面彻底的分离，如JSF或者Tapestry的做法，让页面更为单一和纯净。这可能是最理想的做法，也有更好的对页面组件的重用性。但是当前台页面异常复杂，需要浏览器端较多javascript操作，特别是AJAX的引入，势必有越来越多的页面逻辑转移到客户端，从而破坏了整个架构的一致性，让扩展更难处理。

这说明B/S架构中的逻辑存在于浏览器端和server段是导致系统复杂的根本原因。  
C/S就没有这样的烦恼。  
为什么呢？

对旧技术的补丁，最好的技术是GWT，其将逻辑集中于java代码中（虽然客户端的代码和server端的代码还是分开的）。  
而新的技术，则是RIA。