---
title: '开源的风扇控制器 &#8211; tinyFan'
author: Fan Fan
layout: post
permalink: /2013/01/06/open-source-pc-fan-controller-tinyfan/
aktt_notify_twitter:
  - no
categories:
  - 电子制作
---
## 0）为什么会有这个东西？

一个既有不错性能又安静的机器一直是我纠结的地方。对于PC风扇，主板一般提供了调速接口，这是因为主板自带监控芯片，比如常见的nuvoton，一般能控制最多三个风扇，更多的则要买更贵的主板。对于我，只是需要额外的接口来控制风扇而已：不仅要能降低风扇转速，而且能监控风扇转速，也就是说需要有软件的客户端。市面上有些简单的手工控制风扇，调节不方便不说，也没有办法查看风扇的转速。国外的有比如aquaero 5 LT USB fan controller，不过国内难以买到。种种纠结，一年后，便有了这个东西。

[<img class="alignnone size-medium wp-image-1401" title="IMG_2989" src="http://fkpwolf.net/WordPress/wp-content/uploads/2013/01/IMG_2989-300x225.jpg" alt="" width="300" height="225" />][1]

这个电子小制作主要用来监控PC机箱风扇的转速，本身基于三个开源的项目：[V-USB][2]、[Intelligent Fan Controller][3]、[Open Hardware Monitor][4]。

Intelligent Fan Controller提供了基本的电路和代码思路，我把PIC的MCU换成了AVR的方案，初衷是AVR更开放点。但是AVR提供USB接口的MCU很少，于是使用了V-USB来软件模拟USB，这样成本也更低点。然后桌面软件控制我使用了Open Hardware Mointor，这个功能更强大，能全面监控PC，也容易扩展。我的代码放在[GitHub][5]上面。

## 1）风扇

这里控制的是机箱用的无刷风扇，分为3PIN，4PIN两种。3Pin的使用电压调节，4Pin的使用PWM调节。PWM有更广的调整范围，而电压调节的话电压不能太低，因为很多风扇都有最低的启动电压。所以建议还是使用PWM风扇。这两种风扇都带一根额外的测速线。

## 2）电路

3PIN和4PIN风扇的切换。atmega48提供3个PWM通道，其中一个用来计算时间，其他两个用来控制风扇。对于4PIN规格的PWM风扇，可以直接使用ATMEGA48来控制风扇转速，而对于3PIN风扇，则只能使用控制电压的方式。而控制电压也是使用PWM信号的占空比来控制bulk convert芯片。所以既然都是PWM控制，如何能做到一个风扇接口可以适用于两种不同规格的风扇。我想到的方案是通过逻辑门开关开进行切换，使用一个SPDT和一个DPDT来进行三路的电路切换。其实这个创新点并没有收到好的反响，大家好像都使用3PIN的风扇。后来发现ARM有端口复用，和上面的类似。

## 3）固件代码

转速检测。风扇调速线输出PWM信号，可以简单的认为风扇每转一圈就会发送个高平信号，然后我们计算每分钟的高平信号数量就可以得到风扇的RPM。注意的地方：1）端口要上拉电阻，；2）使用稳定电源测试，否则都容易导致测出来的速度异常。这是因为电路中没有对12V输入进行稳压调整，不过输出到风扇上面的倒有；3）信号去抖动，这是指去掉调速线里面的噪音信号。代码里面用的是软件方法，后来我发现也没有很大必要，因为使用的是稳定的电压，然后对转速准确度要求也不高。

AVR相当的开放，编译环境和代码上传都可以很容易得到，而且跨平台。后来使用了其他的工具，比如开发ARM的Keil，还是觉得avr-gcc+make+avrdude开发效率很高，一个命令行搞定。

## 4）客户端

这是个.Net的客户端，使用C#编写，所以上手还是蛮容易。不过原作者的代码对于新设备的处理有些搓：每加个新设备都必须修改主干代码，而没有类似plug-in的结构，还好.Net比较容易上手，改就改吧，毕竟原作者积攒了不少对各种设备支持。

## 5）制造

PCB画图。我用Eagle，免费的PCB制作软件。

PCB焊接。你需要一个恒温焊台。

代码烧录。为了提高效率，而且目标板上面ISP默认是没有焊接的，所以这里我直接使用烧录器。因为V-USB使用了外接的振晶，所以有时候如果先做了make fuse，再做make flash的话会出现找不到芯片的情况，这个时候，烧录器上的振晶就起了作用。

## 6）菜鸟的启发

快速的出错。快速的迭代。较小的失败代价。

对于菜鸟来说，一切都是新的，一切都需要尝试。所以必须降低每次尝试的痛苦。1）使用Arduino。这个是AVR完全兼容的，你可以把他当作一个AVR开放板。他可以一个鼠标点击触发“编译-》上传-》MCU重置”。省去了很多手工步骤。2）出现问题不要死脑筋。在自己不熟悉的领域要习惯求教别人。很多情况下，提问的人会在讨论的过程中自己发现解决办法。3）抄袭。没有抄袭，寸步难行。比如arduino的电路板文件，很多地方可以借鉴。所以要选择开源，因为开源意味着不仅代码开源，工具和流程也是开源的（生态社区），菜鸟可以从中学到很多东西。4）对于电路板问题，调试很重要，也很难。我的经验是如果有问题，重新做一个；或者先放一放，过会再看。反反复复的尝试有时候会让人筋疲力竭。

一个全新领域给人的能给人不同的视角和全新的启发，虽然很多挫折，但任然值得一试。

 [1]: http://fkpwolf.net/WordPress/wp-content/uploads/2013/01/IMG_2989.jpg
 [2]: http://www.obdev.at/products/vusb/index.html
 [3]: http://geoffg.net/fancontroller.html
 [4]: http://code.google.com/p/open-hardware-monitor/
 [5]: https://github.com/fkpwolf/tinyFan