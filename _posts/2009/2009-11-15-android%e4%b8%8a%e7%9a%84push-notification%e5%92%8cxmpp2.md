---
title: Android上的Push Notification和XMPP(2)
author: Fan Fan
layout: post
permalink: /2009/11/15/android%e4%b8%8a%e7%9a%84push-notification%e5%92%8cxmpp2/
dsq_thread_id:
  - 604187604
categories:
  - Mobile
---
**既然有了这么好的通知工具（异步的哦），那一般的app如何使用这个服务呢？**

可惜的是由于这部分是闭源的代码，而且SDK中这个服务也是undocument的（这是指1.6中的情况，2.0好像加上了不少同步的功能，待查）。消息我们倒是可以收到的，使用Android里面Reciver的东西，Intent为android.intent.action.GTALK\_DATA\_MESSAGE_RECEIVED，具体可以参考源代码里面的SubscribedFeedsBroadcastReceiver这个类。但是如何发生消息呢？发到GTalk么？实验后无果，Android把这些消息都当作chat类型的消息了，反正那个Reciver是收不到的。

我们可以另起炉灶用Smack库做一个XMPP的客户端，这个客户端要作为一个Service运行。<a href="http://devtcg.blogspot.com/2009/01/push-services-implementing-persistent.html" target="_blank">Push services: Implementing persistent mobile TCP connections</a>这里提供了一个例子，实现了一个保持连接状态的Socket连接。按照这篇文章里面的说法，Android系统会隔一段时间后kill掉service，然后启动service，更关键的是在手机CPU处于sleep时，是接受不到push过来的数据，这时候必须使用AlarmManager来定时叫醒CPU（无亲自验证）。

对于Android使用Smack包，<a href="http://davanum.wordpress.com/2007/12/31/android-just-use-smack-api-for-xmpp/" target="_blank">Android – Just use Smack API for XMPP</a>这篇文章有详细描述。虽然Android里面包含了Smack，但是还是不能直接使用（可能对Smack做了修改）。同样对Smack我们也要打上patch才能在Android下面是用，前面的文章中有提供patch，查看了下，主要是把xmlpull的xmlparser换成了kxml2（这个应该是Android内置的）。diff对新的Smack代码也可是使用。

综合上面两部分的代码，就作出了一个持续连接的XMPP客户端。