---
layout: post
title:  "Google Fuchsia"
date:   2016-8-14 14:20:33
typora-root-url: ../../../blog
---

Fuchsia：Google新开源的操作系统 <http://www.infoq.com/cn/news/2016/08/fuchsia> 

fuchsia | AmE ˈfjuʃə | noun 倒挂金钟 

<https://fuchsia.googlesource.com/>

<http://www.infoq.com/cn/news/2015/11/Flutter-Google>

The book <https://fuchsia.googlesource.com/docs/+/master/the-book/> 

内核 LK little kernel <https://github.com/littlekernel/lk> 

使用Dart作为开发语言，说是为了保障性能，然后是自己的图形渲染引擎（这个图形引擎可以独立出来然后放到Fuchsia里面），不基于WebView或者原生控件，都是自己画的。日。又搭上了Material风格，我喜欢。 

<https://flutter.io/> for iOS and Android <https://flutter-io.cn/> 中文社区 

Flutter beta 1 重磅发布：开发精美的原生应用 <https://developers.googleblog.cn/2018/02/flutter-beta1.html> 

似乎和 React Native 有点一样：都是跨平台的移动开发平台。但是[区别在于](https://hackernoon.com/whats-revolutionary-about-flutter-946915b09514) Flutter 没有了 JavaScript 桥，直接 Dart 编译，性能会好点。更重要的是 Flutter 没有使用 phone OS 原生的 widget，而是自己画的！所以并不能成为 “Native”。有个图很清楚（前者为 React Native）： 

![flutter-1](/images/2016/flutter-1.png)

![flutter-2](/images/2016/flutter-2.png)

但其摒弃了传统的 CSS 布局，自己搞了一套，everything is a widget，于是又了 Row/Column widget。是不是像 React component？ 

Fuchsia能在树莓派上面运行一个Material图形界面么？Google此举是为了摆脱linux和x.org么？看来linux对于实现一个现代的图形界面还是太笨重？和x.org捆绑太紧？据说可以在Raspberry Pi 3上面跑，但是还没看到。 

这帮人真是精力过剩啊。这一套(flutter)整个的Get Started系统很值得学习！ 

Escher is a physically based renderer 

Flutter由C、C++和Dart语言编写，也包含了Skia 2D渲染引擎和Blink的文本渲染系统.Fuchsia直接用了Escher? 

为什么Flutter会选择 Dart ？<http://www.infoq.com/cn/articles/why-flutter-uses-dart> 

深入了解Flutter界面开发 <https://yuque.com/xytech/flutter/tge705> 闲鱼团队 

支持设备 <https://fuchsia.googlesource.com/docs/+/HEAD/fuchsia_paver.md> 

* Acer Switch Alpha 12 
* Intel NUC 
* Google Pixelbook 

 如果这些设备不像树莓派或者安卓机那样便宜，则其无法流行。 

<https://arstechnica.com/gadgets/2018/01/googles-fuchsia-os-on-the-pixelbook-it-works-it-actually-works/> 感觉图形界面复制了一个 Chrome OS。而且界面似乎很多是给手机用的 

如果 Fuchsia 可以成功，主要在于其高效的界面系统。我想这个是其在开发 Chrome OS 中感受到 X window 的累赘而突发灵感的。对于图形系统，不存在小的重构，因为会复杂到不如推到从来，而这个复杂主要是遗留系统导致，这让人沮丧。那为什么要开发新的内核呢？纯粹配合 UI？ 

### Dart

<https://www.dartlang.org/>

Dart 2 正式发布：专门针对 Web 和 App 的开发而优化 <https://www.oschina.net/news/93601/announcing-dart-2?from=20180225> 

Google 宣布重启 Dart 编程语言，应对程序开发挑战  <https://www.oschina.net/news/93640/google-announces-reboot-programming-language-dart?from=20180225> 

Dart 语言的介绍 <https://www.yoytang.com/dart-intro.html> 

似乎用来取代JavaScript，作为一种界面语言，显然更像 JavaScript，而 Go.lang 更像 C 语言，连闭包也没有。 

<https://webdev.dartlang.org/> 原来也推广到 AngularDart，可以编译成 JavaScript？那其依赖库也编译成了 JS？这个工作量不小。Go.lang 的标准库据说很强大。 

| Mobile | Create an app from a single codebase that runs on both iOS and Android. | Flutter |
| ------ | ------ | ------ |
| Web    | Create an app that runs in any modern browser. | Dart webdev |
| Server | Create a command-line tool or server.| Dart VM|

Ref <https://www.dartlang.org/guides/get-started>. 野心很大。前两种容易理解，server 端这是要和 Go.lang 竞争？ 

WebStorm 支持 Dart 语言，看来把Dart 作为 UI 开发语言了。 

一种语言如果脱离了生态环境则只能拿来学习，但是如同英语，如果没有伟大的英语小说，也没有学习的必要了。所以语言只是载体。对于 Java，其生态圈就是多年的企业应用环境，和 C 语言一样，是难以代替的，除非企业应用环境消失，大家都用 SAAS 了。但是对于特定业务的开发和定制，这种琐碎的事情还是只有 Java 更有开发效率（考虑到开发人员的经验、已有框架、IDE 支持），其他语言可能表达力更强，但是在漫长乏味的项目管理中优势已经不大。 

### Raspberry pi
Flutter on Raspberry Pi (mostly) from scratch <https://medium.com/flutter-io/flutter-on-raspberry-pi-mostly-from-scratch-2824c5e7dcb1>
[Flutter 1.0重磅发布，它还想做桌面和Web开发](https://mp.weixin.qq.com/s/dRHHn9vtCsYBgq2RrVlDAQ)