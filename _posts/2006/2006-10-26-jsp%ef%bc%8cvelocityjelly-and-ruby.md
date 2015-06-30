---
title: JSP，Velocity,Jelly and Ruby
author: Fan Fan
layout: post
permalink: /2006/10/26/jsp%ef%bc%8cvelocityjelly-and-ruby/
blogger_blog:
  - fkpwolf.blogspot.com
blogger_author:
  - fannoreply@blogger.com
blogger_permalink:
  - /2006/10/jspvelocityjelly-and-ruby.html
categories:
  - Web Development
---
项目中要动态产生文档，开始用JSP做了个简单原型，后来觉得代码比较混乱，换上了Velocity：一种模板语言。 虽然大同小异，但有些地方还是很方便。比如原来jsp中：  
`<%=person.getPostion()%>`  
现在只要$person.position就可以了，少了很多讨厌的百分号。还可以定义function，以及其他一些你能想到的特性。很有点脚本语言的味 道，有如prototype.js的$()，更符合编程思路，大家都喜欢，觉得这样才自然（prototype这里大概从ruby中汲取了灵感）。  
还有另外一个类是的库jellyXML-based scripting and processing engine。使用了一种XML和${}的混和语法。比如:  
`<br /><j:forEach items="${IMPORTS}" var="i" trim="false"><br />import ${i};</j:forEach><br />`  
而velocity则较为一致：  
`<br />#macro( getBox $thisboxvalue $boxvalue )<br />#if ( $thisboxvalue == $boxvalue ) a #else b #end<br />#end<br />`  
单从语法上讲，jelly类似jsp + jstl。While Jelly has found some success in projects such as Maven, the general consensus is that XML is a poor choice for a programming language even the creator of Jelly apologized at <http://radio.weblogs.com/0112098/2004/03/26.html#a472>.  
其实ANT用XML作语言也有同样的问题。TheServerSide上有篇文章<a href="http://www.theserverside.com/news/thread.tss?thread_id=42293" target="_blank">Allen Holub: Just Say No to XML</a> 说明了这一点。 那为什么jsp不做成这样子呢？连rails也是这种讨厌的风格？（还好ruby的语法很方便）个人认为一则效率的考虑：velocity大概有个预先装入、解析、预编译的过程（待考证），还会将结果放在cache中。二则（更主要）多了一种语法，造成复杂和重复。  
velocity还有些设计上的亮点：使用中遇到一种情况，从bean中取出属性后要作个代码页转换然后在页面上显示。由于原来bean的getXXX方 法都已经产生好，手工改费力不讨好。这时参考velocity文档时发现有一个get()的方法：当对于属性A，bean不存在getA()方法时，调用 bean的该方法。nice! 让人想到了ruby也有类似设计：Object有个method_missing的方法.也是很好的idea，方法简单但是效果很好。（参考我的blog:[Ruby的method的动态特性][1] ）  
从脚本语言看，<a href="http://groovy.codehaus.org/" target="_blank">Groovy</a>之类从语法上更为强大，也更复杂。如果Java有成熟的、广泛接受的脚本语言技术，velocity大概也无立足之地了。  
另： [Roller Weblogger][2]的模板也是用了Velocity技术。

 [1]: http://blog.matrix.org.cn/page/fkpwolf?entry=ruby%E7%9A%84method%E7%9A%84%E5%8A%A8%E6%80%81%E7%89%B9%E6%80%A7
 [2]: http://www.rollerweblogger.org/