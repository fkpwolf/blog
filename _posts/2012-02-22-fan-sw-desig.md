---
title: 风扇软件设计
author: Fan Fan
layout: post
permalink: /2012/02/22/fan-sw-desig/
dsq_thread_id:
  - 615095903
categories:
  - 电子制作
---
准备用Open hardware mointor，代码在这里：<http://code.google.com/p/open-hardware-monitor/>。 Google Code就是烂，Fork没有，Clone也没有，不知道怎么check-in code，必须成为committer么？好像有把Google Code同步到GitHub上的工具，而且也可以merge。

1. 如果要添加新硬件，必须修改Hardware/Computer.cs，手工的add，比如Add(new Heatmaster.HeatmasterGroup(settings))，没有类似FakeSMC.kext插件的形式。

2. 整个代码是用c#写的，既然是操作硬件的，为啥不用c呢？可能界面是个大问题吧。不过这个项目已经支持不少的硬件包括一些新的芯片和主板，这个是关键。所以将就用吧。

3. 操作USB的方式。直接用c#应该也可以，因为操作主板LPC都可以，何况小小USB。可能是借助于WinRing0这个库。另外一种方式是使用libusb这个开源而且跨平台的库，独立出一个层来，接口有得到RPM、设置风扇duty。而且把USB的通讯报文的处理也封装在这个里面。好处是这样这个层也是跨平台的。对于其他的open hardware mointor不支持一些平台，比如Mac，可以暂时使用命令行的方式来控制风扇。

&#8230;&#8230;&#8230;.