---
title: Android 2.2终于开放了推送的消息服务
author: Fan Fan
layout: post
permalink: /2010/05/22/android-2-22-push-message/
aktt_notify_twitter:
  - no
dsq_thread_id:
  - 604105238
categories:
  - Mobile
tags:
  - android
---
内流满面阿，终于不用自己搞个长连接了。

2.2释放出来了，在其新特性中，有一点：Cloud to Device Messag([C2DM][1])，这个跟gtalk类似，可以让web程序或者app engine向手机发送消息，不同的是gtalk是系统级别的，一般程序访问不了，这个则是标准的API。

在旧版本android上实现这个功能还是蛮麻烦的，我原来[写过][2]。主要的思路就是自己起一个gtalk的客户端，用xmpp协议，然后放在service保证长时间运行。缺点很明显：这个service消耗手机资源，功能跟系统本身服务重复。不过在消息服务没有开放时，自己的app是接收不到xmpp消息的。

google开放出来很直接，因为使用的还是老一套。下面是手机受到消息时的log，可以看出使用的还是smack，当然，协议还是xmpp了。

05-22 09:26:13.356: DEBUG/GTalkService(223): [ChatMgr] processPacket: not logged in! drop org.jivesoftware.smack.packet.DataMessage@43e929e8  
05-22 09:26:13.576: INFO/JumpNote_SyncAdapter(298): Beginning full sync for account fkpwolf@gmail.com  
05-22 09:26:16.316: INFO/JumpNote_SyncAdapter(298): Sync complete, setting last sync time to 1274491573563

那么web端或者云端如何发送消息呢？我看到代码里面是访问https://android.clients.google.com/c2dm/send，大概用的是rest吧。不过现在google要申请帐号才能访问这个服务。mobile收消息则使用标准的receiver模式。

Google给了个例子：<a href="http://code.google.com/p/jumpnote/" target="_blank">JumpNote</a>，可以看作双向同步功能的写字板。技术用android client + app engine + gwt（web client），使用了android新的sync API，还是一个蛮复杂的例子，因为这不单单是一个mobile app，而是一个完整的云服务实例，值得仔细研究研究。

<img class="alignnone size-full wp-image-770" title="device" src="http://fkpwolf.net/WordPress/wp-content/uploads/2010/05/device.png" alt="device" width="320" height="480" />

 [1]: http://code.google.com/intl/zh-CN/android/c2dm/
 [2]: http:///2009/11/15/android%E4%B8%8A%E7%9A%84push-notification%E5%92%8Cxmpp1/