---
title: JSF Custom Compoment development tips
author: Fan Fan
layout: post
permalink: /2008/07/03/jsf-custom-compoment-development-tips/
blogger_blog:
  - fkpwolf.blogspot.com
blogger_author:
  - fannoreply@blogger.com
blogger_permalink:
  - /2008/07/jsf-custom-compoment-development-tips.html
dsq_thread_id:
  - 
categories:
  - JSF
---
1. JSP taglib  
In JSF, the scriplet like &#8220;#{RequestBean1.name}&#8221; is treated as a <span style="font-style: italic;">ValueExpression</span> field in tab class. The corresponding tab lib definition file should nests the attribute type with <span style="font-style: italic;">deferred-value</span>.

And also in tld file, <span style="font-style: italic;">jsp-version 2.1</span> element should be included. If this scriplet is missed, some container like Tomcat 6.0.16 will take this tag as generial tag and if we invoke <span style="font-style: italic;">isLiteralText</span>() on the <span style="font-style: italic;">ValueExpression</span> object, <span style="font-style: italic;">true </span>will return. It was not excepted. But the GlassFish V2 ignored this and took 2.1 as default.

2. Why setProperties() in tag handler java is not invoked?  
You should define all property of jsf component java in faces-config.xml. Sometimes it looks like that a attribute is defined twice: one in tld, one in faces-config.xml. [JSF Template][1] can make these things easy by generating all configuration file. In order to archive this, it introduces some anonations.

3. Why did some data shown on page disappear after re-loading the page?  
implement the <span style="font-style: italic;">saveState/restoreState</span> method.

 [1]: https://jsftemplating.dev.java.net/