---
title: webOS程序的调试
author: Fan Fan
layout: post
permalink: /2011/05/29/webos-debug/
dsq_thread_id:
  - 610246959
aktt_notify_twitter:
  - no
categories:
  - Mobile
  - Web Development
---
对于web开发，Firebug之类的工具必不可少。webOS的SDK里面自带了Palm Inspector的调试程序，不过我反复尝试都没有调试成功，一直提示“Disconnected. If you get disconnected as soon as you start Palm Inspector, make sure that the app is ready for inspection.&#8221;。可是我已经通过命令&#8221;palm-launch -i com.mycompany.palm1&#8243;使app运行在inspector的模式了，奇怪的很。

Google一下，发现有种新的调试模式：[Emulator Host Mode][1]，可以直接用桌面Chrome来运行app，文档上说的是用PuTTY，我摸索了下，配置如下：

[<img class="alignnone size-full wp-image-981" title="palm2" src="http://fkpwolf.net/WordPress/wp-content/uploads/2011/05/palm2.png" alt="" width="615" height="546" />][2]

这样，ssh到localhost:5522，帐号：root/[enter]，然后在Chrome访问http://localhost:5580/。如下：

[<img class="alignnone size-full wp-image-982" title="palm1" src="http://fkpwolf.net/WordPress/wp-content/uploads/2011/05/palm1.png" alt="" width="362" height="510" />][3]

可以使用Chrome的Developer Tool来查看dom对象，Cool! 看来webOS系统本身有个web server运行在8080端口上，而UI则是用webkit访问这个server的结果，这个web服务是自给自足啊！不过一些本地功能，比如“打电话”，在Chrome中显然使用不了，所以这个工具只是用来调整UI用的。

新的框架Enyo好像独立出来了，可以直接在本地开发。

[2013.1.6] Chrome for Android也有类似的[调试模式][4]，同样用端口转发。不过是调试Mobile Web页面，比如下面这个是查看Youku的HTML5 video元素。UI在真机上，调试控制台在desktop chrome上。

[<img class="alignnone size-medium wp-image-1396" title="device-2013-01-06-142739" src="http://fkpwolf.net/WordPress/wp-content/uploads/2011/05/device-2013-01-06-142739-168x300.png" alt="" width="168" height="300" />][5]

 [1]: https://developer.palm.com/content/api/dev-guide/tools/emulator.html#using-emulator-host-mode
 [2]: http://fkpwolf.net/WordPress/wp-content/uploads/2011/05/palm2.png
 [3]: http://fkpwolf.net/WordPress/wp-content/uploads/2011/05/palm1.png
 [4]: https://developers.google.com/chrome/mobile/docs/debugging
 [5]: http://fkpwolf.net/WordPress/wp-content/uploads/2011/05/device-2013-01-06-142739.png