---
title: why Ruby over Java
author: Fan Fan
layout: post
permalink: /2007/04/04/why-ruby-over-java/
blogger_blog:
  - fkpwolf.blogspot.com
blogger_author:
  - fannoreply@blogger.com
blogger_permalink:
  - /2007/06/why-ruby-over-java.html
categories:
  - Rails
---
这个[帖子][1]很不错，可以参照来学习Ruby。总结来说Ruby的优点在于：  
* closures  
* multiple implementation inheritance via mixins (simplifies a lot designs where we would use decorators and factories in java)  
* messages to objects as if they were first class methods (natural way of doing AOP, used a lot in ActiveRecord). This actually also counts for the famous java properties support planned for java 7&#8230;  
缺点在于没有了Java的类型检查，而这是提高Java开发效率的关键：通过编译就可以排除大部分的错误，也提高了系统的安全性。但是在开发一个完整的 Java应用时，经常会遇到DSL语言的地方，比如jsp, sql，这些都是没有办法进行类型检查的地方（DSL的特点吗？）。再比如对Session的处理不可避免的会用到类型cast。  
在RJS里面，html, rjs, rb这些文件之间存在的变量type和name的合约（或契约）更多，而只能手工检查。  
疑问：Ruby里面method的参数的类型会在运行时进行类型检查？  
那JRuby会起到一个粘合剂的作用吗？这会提高系统的复杂性，而且一个系统中使用两种语言也少见，除非是一个遗留系统。

 [1]: http://www.javalobby.org/java/forums/t92777.html