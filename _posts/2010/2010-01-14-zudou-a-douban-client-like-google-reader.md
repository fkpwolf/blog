---
title: 模仿Google Reader的豆瓣手机客户端：“煮豆”
author: Fan Fan
layout: post
permalink: /2010/01/14/zudou-a-douban-client-like-google-reader/
dsq_thread_id:
  - 649231623
categories:
  - Mobile
tags:
  - doubanclient
---
一直觉得Google Reader做的不错，Ajax的特则很适合用手机用户：比如bookmark一条记录不用刷新页面，这个对pc来说不是很关键，但是手机网路慢而且不稳定，可能一次刷新页面就再也回不去了，而用户前面一个页面还没有看完，所以“异步”对手机来说很重要。

于是有个这个山寨版的“Google Reader”- [zudou.net][1]，也可以说是向Reader致敬吧。主要特征就是“折叠式”的页面显示，我觉得[Yahoo UI Pattern][2]应该把这个加入它的库中。

主要功能有：

1）摘取douban主页上面的最受欢迎的书评和影评信息（还没有加上cache功能）；

2）显示评论的详细信息，也可以收藏评论的书或电影，暂时还没有回应评论的功能，好像douban API还没有看放这个功能；

3）发表广播和现实友邻广播。

适合的客户端为：Android, iPhone。

<img class="alignnone size-full wp-image-579" title="zudou1" src="http://fkpwolf.net/WordPress/wp-content/uploads/2010/01/zudou1.png" alt="zudou1" width="320" height="480" />

<img class="alignnone size-full wp-image-580" title="zudou2" src="http://fkpwolf.net/WordPress/wp-content/uploads/2010/01/zudou2.png" alt="zudou2" width="320" height="480" />

**1.15 add**: 惊闻Google欲退出中国，痛心！何处为安身立命之所！向Google致敬！

**1.15 add**: 这个功能为什么不加入到Google Reader中呢？因为我想豆瓣也有“最流行书评”的RSS Feed，当热也可以加到Reader中，不过，Reader的“收藏”功能不能收藏豆瓣的书籍，或者说收藏功能只是在Reader中有效（评论也只能在reader的圈子中交流，这里Reader是一个信息孤岛）。douban API的有点像基于“RSS协议”的API，<span style="text-decoration: line-through;">能否做个reader的插件（假如reader用这个功能），当在reader中操作时，可以反映到各个RSS的源，这样想发表评论也不用到真实页面去发表了？</span>操作性比较差，很多网站都要登录后才能发表评论&#8230;

**1.16 add**:iPhone模拟器上的截屏

<img class="alignnone size-full wp-image-587" title="zudou-iphone" src="http://fkpwolf.net/WordPress/wp-content/uploads/2010/01/zudou-iphone.png" alt="zudou-iphone" width="481" height="322" />

 [1]: http://www.zudou.net
 [2]: http://developer.yahoo.com/ypatterns/