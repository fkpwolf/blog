---
title: Questions on Jboss Seam
author: Fan Fan
layout: post
permalink: /2007/01/13/questions-on-jboss-seam/
blogger_blog:
  - fkpwolf.blogspot.com
blogger_author:
  - fannoreply@blogger.com
blogger_permalink:
  - /2007/01/questions-on-jboss-seam.html
categories:
  - JBoss
---
Obviously, seam has many cool features. There are some questions:  
1. Seam introduced many kinds of context, and much more than Spring. Are they better than Spring?  
2. any bean marked with @Name() will be treated as managed component. client just uses &#8220;DumyBean cc = (DumyBean) Component.getInstance(&#8220;dumyBean&#8221;, true);&#8221; to access the bean. Maybe this approach is clearer than Spring xml configuration. Is this approach less efficiently?  
3. debug.seam page is right around thing! It should exist in Spring. Is it a servlet? Where is its configuration?  
4. @Logger looks cool. This annotation is introduced by jboss. How to do this?In org.jboss.seam.Component, there is: if ( field.isAnnotationPresent(org.jboss.seam.annotations.Logger.class) )So, It looks that Logger is a build-in service. There is no way to configure it or add my service, yeah?  
5. eclipse&#8217;s command works very well with Ant? For example: when I click save button, eclipse automatically invokes Ant&#8217;s task. How to do this?6.annotation is largely used in Seam. What about this?