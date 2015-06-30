---
title: JPDA(Java Platform Debugger Architecture)
author: Fan Fan
layout: post
permalink: /2007/08/01/jpdajava-platform-debugger-architecture/
blogger_blog:
  - fkpwolf.blogspot.com
blogger_author:
  - fannoreply@blogger.com
blogger_permalink:
  - /2007/08/jpdajava-platform-debugger-architecture.html
dsq_thread_id:
  - 629731256
categories:
  - methodology
---
How let your changed java code updated to server? It is a big problem in develpping any non-trivial JEE project. 

Resently, I run a JBoss 4.04 outside of Eclipse. The application archive(EAR) is so big that we can not run it in IDE environment. Then the debug process is boring. After having modify the java code, I have to package it, copy to deploy directory and restart the server. Ant can help me a lot but a debug round still takes me much of time.

In this situation, JPDA will useful. With the help of eclipse, I can use it to connect to a &#8220;Remote Java Application&#8221;. You can find it in &#8220;Debug&#8221; dialog. Then you can inspect value of a varible and even modify the java code. <strike>Before that, you have to add a breakpoint to your java code. Only java with breakpoint can be modified and replaced to the server. This is a nice feature when you need modify code many times.</strike> When eclipse has attached remote server, any modified java code could be updated to the server. This is so-called &#8220;Hotswap Bug Fixing&#8221;. Of course the source code should be added to the debug instance&#8217;s &#8220;**Source lookup path**&#8220;. Otherwise the server didn&#8217;t suspend at your breakpoint. And you should mind that the compiled classes were swapped just in the server&#8217;s memory and didn&#8217;t updated to the server&#8217;s file system. So after finished debug, you should copy the classes to the server&#8217;s deploy directory.

Some other approaches I often used include <span style="font-size:100%;">Hot deploy</span> and <span style="font-size:100%;">restart application</span> (not server).

Hot deploy is used to deploy a archive such as .war or .jar(EJB) automatically when you put them in deploy directory. Most servers has this feature.

Restart application or Reload Context is used to reload all resource(classes and configuration) in one application eg EJB, or War. This approach is heavier than others. Tomcat will throw exception &#8220;Out of memory&#8221; after reloaded a web application many times.

To Netbeans, It provides debug mode also. There are some differences on &#8220;hot class replace&#8221; between it and eclipse.

<a onblur="try {parent.deselectBloggerImageGracefully();} catch(e) {}" href="http://3.bp.blogspot.com/_lz-dDaxCZHc/RtAcp8sRDHI/AAAAAAAAACk/XP4-qPIWP-w/s1600-h/Untitled.jpg"><img style="margin: 0px auto 10px; display: block; text-align: center; cursor: pointer;" src="http://3.bp.blogspot.com/_lz-dDaxCZHc/RtAcp8sRDHI/AAAAAAAAACk/XP4-qPIWP-w/s400/Untitled.jpg" alt="" id="BLOGGER_PHOTO_ID_5102609884802780274" border="0" /></a>

Above is netbeans 6M10 debug tool bar. It has a &#8220;Apply Code Changes&#8221; button to submit the changed code to server. It need you do manually. This way is different from eclipse which done automatically.