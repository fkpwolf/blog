---
title: OSGi的白板模式(White board Pattern)
author: Fan Fan
layout: post
permalink: /2009/12/01/osgi-white-board-pattern/
aktt_notify_twitter:
  - no
dsq_thread_id:
  - 610430120
categories:
  - 模式
---
这个模式主要是来对付Listener Patter（或者Observer Pattern），具体来说，后者的缺点在：

  * 产生了很多的类，比如很多情况下，一个event source只对应一个listener，这种浪费在手机这种受限设备上是一个不得不考虑的问题。
  * event source和listener的管理复杂，比如注册和注销，因为这两者可能都很动态，不能保证自己需要对方时对方是工作的，或者说难以达到消息的可靠性。这两者之间的依赖太明显，太直接。

<a href="http://www.osgi.org/wiki/uploads/Links/whiteboard.pdf" target="_blank">White Board</a>引入了event broker，作为两者间的桥梁，保存消息和分发消息，走向了subscribe/publish模式。

<img class="alignnone size-full wp-image-506" title="whiteboard" src="http://fkpwolf.net/WordPress/wp-content/uploads/2009/12/whiteboard.PNG" alt="whiteboard" width="694" height="396" />

图摘抄至<a href="http://neilbartlett.name/blog/osgibook/" target="_self">OSGi in Practice</a>。这并无神奇的地方，只是把复杂的代码挪到了broker。OSGi则在这个地方加上了比如异步，有顺序的分发和可靠的分发这些功能。还有Topic的功能，比如有一个topic: a/b/c/*，这样event source和listener直接就靠这个纽带来进行交互，在Android中把这个抽象成URI，感觉几乎是一样的。

倒不清楚Android是一个OSGi的实现？还是只是把OSGi作为一个templete拿来模仿？