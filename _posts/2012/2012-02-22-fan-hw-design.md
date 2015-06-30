---
title: 风扇硬件设计
author: Fan Fan
layout: post
permalink: /2012/02/22/fan-hw-design/
dsq_thread_id:
  - 603397867
categories:
  - 电子制作
---
1. <del>需要一个测温功能</del>，这样芯片可以有两种模式：一个为软件模式，一个为硬件模式。当为软件模式时，使用USB传递指令，温度可以从tinyFan上取，可以从主板上取。没有USB控制时，fallback到硬件模式，也可以称为巡航模式或者默认模式，这时候温度从本身自带的测温模块上取。这样做主要是让比较懒的用户拿到就可以用。有些复杂，可以先在PCB上留出预留的针脚，软件再慢慢搞起来。

2.需要一个LED来显示PCB的工作状态。现在考虑这个LED能否显示有用的信息，否则只是通电/断电没有多大意思。另外taobao上面多色的LED还没找到。

3. 风扇有3pin和4pin之分，但是对于avr来说基本一样：都需要一个pwm来调速，要用直接调，要么通过调电压来调。所以如果一个风扇接头只能使用3pin或者4pin，那么就浪费了一个pwm信号。所以最好是有种切换的方法来适应两张接口。DPDT双刀双掷符合这种要求，但是DPDT SMD的taobao上没有，而且感觉都很大，如果有四个风扇接头，则需要四个DPDT，好恐怖，而且感觉按钮很土。后来发现有种芯片可以做到：74HCT4053，TTL enable，3 * SPDT，这样两个就能组成3个DPDT，而且价格便宜，来源广泛。就是不知道这东东是否受得了PWM的高频要求。其实我只是需要连通，所以频率可能不是问题。

4. [*add 8.29*] 支持大功率水泵。现在的电源转换芯片每个针脚最大500ma，接上两个最大就是1A。对于风扇来说很大了，但是对于上水的发烧友来说，就得3A才能带得动水泵。[TMS-200/205 Aquaero 4][1]这个电控用的是三个针脚的怪物芯片就搞定了，型号未知。这类东西叫[DC Voltage converter][2]，介绍的[DC-DC Converter Tutorial][3]，能搜到一大堆。比如[Allegro][4]的，比如[Linear][5]的，贸泽上面也有[一大堆][6]。不过最大的问题是不清楚如何去控制，PWM么？还是可变电阻（电位器）？datasheet看不懂啊，而且好像还需要附加电路，这样现有电路就得大改了。taobao上[有卖][7]一种LM2596的稳压模块，不过这个要加上散热器，感觉比起牛牛的那种要笨重。

5. 更换MCU。现在版本用的是atmega8，3个PWM通道，带动个风扇。如果升级成atmega88，则有6路通道，而价格6￥，才贵了2￥不到。这样至少能带动10路风扇，而且也没有必要分3pin和4pin之类，直接5个4pin，5个3pin就可以了。每个1A，则大4pin输出功率为120W，可以么？

&nbsp;

 [1]: http://www.chiphell.com/forum.php?mod=viewthread&tid=269624&highlight=aquaero
 [2]: en.wikipedia.org/wiki/Voltage_converter
 [3]: http://www.maxim-ic.com/app-notes/index.mvp/id/2031
 [4]: http://www.allegromicro.com/en/Products/Regulators-And-Lighting/Single-Output-Regulators.aspx
 [5]: http://parametric.linear.com.cn/step-down_(buck)_regulators#!cols_2167,1032,1033,1107,1105,1038,1036,1367!s_1107,1!gtd_
 [6]: http://www.mouser.cn/Semiconductors/Integrated-Circuits-ICs/Power-Management-ICs/DC-DC-Switching-Converters/_/N-6j76u?Ns=Pricing%7c0&P=1z0wd5eZ1z0wdj3
 [7]: http://item.taobao.com/item.htm?id=16645279046