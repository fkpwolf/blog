---
title: Visual library of NetBeans
author: Fan Fan
layout: post
permalink: /2007/06/02/visual-library-of-netbeans/
blogger_blog:
  - fkpwolf.blogspot.com
blogger_author:
  - fannoreply@blogger.com
blogger_permalink:
  - /2007/06/visual-library-of-netbeans.html
dsq_thread_id:
  - 710674944
categories:
  - NetBeans
---
Today I evaluated [Visual library][1] of NetBeans org. There are not too much information about it on Sun&#8217;s site. And it is a part of NetBeans 6 M9. I guest it was just separated from Netbeans as a standalone project. It&#8217;s many feature is same as JGraph which has been used by many users. But I think this library provides more about UI design but not only concerning on graph as done by JGrah. For example, it has a feature &#8220;ComponentWidget &#8211; AWT/Swing components on scene&#8221;. So you can do this:

`JButton button = new JButton();<br />button.setText("xixi");<br />ComponentWidget widget = new ComponentWidget(this,button);` 

Then I have a question: Is Visual Web (netbeans) developed on this library? If so, Visual Library is very cool! (From [this post][2], just page flow navigator is based on Visual Library.)  
There is a [video][3] which created a demo on Visual library. You can find that a JasperReports tool called [jarvis][4] is also base on this library. It looks powerful.

 [1]: http://graph.netbeans.org/
 [2]: http://www.javalobby.org/java/forums/t94658.html
 [3]: http://www.netbeans.org/download/flash/netbeans_60/jl_preso_vislib/player.html
 [4]: https://jarvis.dev.java.net/