---
title: 自制BeagleBone LCD Cape记录
author: Fan Fan
layout: post
permalink: /2012/06/10/%e8%87%aa%e5%88%b6beaglebone-lcd-cape%e8%ae%b0%e5%bd%95/
categories:
  - 电子制作
---
LIDD模式很痛苦（好想只是为8位MCU准备的），还是换成raster模式吧。[AM335x LCD Controller Driver&#8217;s Guide][1]谈的就是这个模式，用的da8xx-fb driver，这个在驱动好像linux kernel自带。

这两种模式有啥区别呢？LIDD仿佛是LCD本身要自带驱动芯片，比如ssd1289。raster模式则对LCD来说是兼容的，也就是说da8xx可以处理多种LCD，只需简单的配置。

做了3.2寸的LCD，发现对于开发板来说，如果自己连线太难看，线太多，最后的成品基本不能用来平常使用。还是得有个cape。[beaglebonetoys][2]已经有7寸和3.5寸的lcd cape。7寸的有资料可以下载，3.5寸的还没有（后来在一个[商店][3]发现了原理图，也是有transceiver的chip，这个需要类似ssd1289.c驱动么？）。

BeagleBoneToyes的3.5寸使用的LCD型号[wiki][4]上说是Elpa Displays S035Q01，但是[0026-beaglebone-fix-3.5-lcd-cape-support.patch][5]中CDTech_S035Q01，一样的型号，不同的厂商？cdtech的这个在taobao上有卖，48￥。从这个patch上看驱动的代码很少，基本就是个配置的过程。或许在raster模式下，LCD型号并不重要。7寸的cape有详细的[资料][6]，看了下电路图，还蛮复杂的。主要有3个芯片：1）带buffer的transceiver，**BGA**封装；2）2个电源管理IC；3）EEPROM，存储cape信息。都用的是TI的芯片，相当无语。

[LCD connectivity][7]这个谈到了raster的种类：parallel（夏普的LQ043就是这个类型）；LVDS；TDM。

LIDD我的理解是显示器包含内部的frame buffer，所以LCD刷新和变化的时候不用MCU发送整个屏幕的数据，MCU只用发送一个command（先要选择模式为command模式），比如改屏幕为横屏显示。这个比较智能点。

> – Character Displays &#8211; Uses LCD Interface Display Driver (LIDD) Controller to Program these Displays
> 
> – Passive Matrix LCD Displays &#8211; Uses LCD Raster Display Controller to Provide Timing and Data for Constant Graphics Refresh to a Passive Display
> 
> – Active Matrix LCD Displays &#8211; Uses External Frame Buffer Space and the Internal DMA Engine to Drive Streaming Data to the Panel

上面是AM335x summary种支持的显示类型。其中被动的现在已经很少用，主动的就是一般的TFT。看来raster的TFT也有frame buffer，是和内存中的一块区域一一对应的，然后用DMA来传输数据。这样除了TTF的buffer，linux也有一部分的内存作为frame buffer？

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;

Raspbery Pi的显示输出接口就丰富多了。除了HDMI，还有[DSI][8]接口。如此看来，BB真是失败啊。

 [1]: http://processors.wiki.ti.com/index.php/AM335x_LCD_Controller_Driver's_Guide
 [2]: http://beagleboardtoys.com/wiki/index.php?title=Main_Page
 [3]: http://www.tigal.com/product/2628
 [4]: http://beagleboardtoys.com/wiki/index.php?title=BeagleBone_LCD3
 [5]: https://github.com/Angstrom-distribution/meta-ti/blob/master/recipes-kernel/linux/linux-ti33x-psp-3.2/beaglebone/0026-beaglebone-fix-3.5-lcd-cape-support.patch
 [6]: http://beagleboardtoys.com/wiki/index.php?title=BeagleBone_LCD7
 [7]: http://processors.wiki.ti.com/index.php/LCD_connectivity
 [8]: http://elinux.org/RPi_Screens#DSI_port