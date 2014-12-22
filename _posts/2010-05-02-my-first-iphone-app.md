---
title: 第一个iphone小程序，哈哈
author: Fan Fan
layout: post
permalink: /2010/05/02/my-first-iphone-app/
aktt_notify_twitter:
  - no
categories:
  - Mobile
---
<img class="alignnone size-medium wp-image-737" title="Screenshot 2010.05.02 14.48.52" src="http://fkpwolf.net/WordPress/wp-content/uploads/2010/05/Screenshot-2010.05.02-14.48.52-200x300.png" alt="Screenshot 2010.05.02 14.48.52" width="200" height="300" />

瞎鼓捣出来的程序：豆瓣的客户端。不过这次是iphone的本地程序，还是个雏形。由于server端调用采用的都是json数据格式，所以换个client很容易，真正的MVC模式啊。不过iphone本身好像不带json支持，还得自己[下载][1]个，看来还是android扩展好点。

iphone开发感觉还是很怪异的，objective c还要自己管理内存，alloc & release，啥啊。UI用这，server端用ruby，很不和谐啊，反过来还差不多。而且这种语言还不是标准c，加了很多扩展，N多关键字。不过人家UI牛就是没办法，动画效果无处不在（osX积累多年噢），**这样才让本地应用比较html有了存在的理由**。

 [1]: http://www.mobileorchard.com/tutorial-json-over-http-on-the-iphone/