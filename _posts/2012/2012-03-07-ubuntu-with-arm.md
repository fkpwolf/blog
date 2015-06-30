---
title: Ubuntu with ARM
author: Fan Fan
layout: post
permalink: /2012/03/07/ubuntu-with-arm/
dsq_thread_id:
  - 603394575
categories:
  - 电子制作
---
陡然想看看UBUNTU能否跑在ARM上面。[这里][1]有总的信息，因为ARM的种类也比较多，所以不是所有的都能跑ubuntu。先要确定是Linux很早就能跑在ARM上了，但是ubuntu这种桌面系统不光只有kernel，还有很多其他的软件。

现在能支持的ARM一般是[OMAP][2]平台的，比较新的是OMAP4，双核。kindle fire用的也是这个U，其他平板还有国产的“智器 Ten3”。智器优于kindle fire在于屏幕大，而且已经支持android 4了，便宜200元。不过社区支持可能没有kindle fire好，因为搞的人少啊。

//今天又看到一东东，刚好符合我的要求 &#8211; [Raspberry Pi][3]，这个是为小孩子准备的超低价电脑，跟前些年的那个啥“一人一台PC”有点像。[基于ARM][4]，有HDMI输出，不带显示器，39$，开发板样子和ARDUION很像，我喜欢，就是一时半会可能买不到。

//专门的开发板有BeagleBoard和pandaBoard，后者功能比较强，基于OMAP4的双核，带HDMI+DVI输出。前者则是普通的ARM A8，优点在于社区成熟，容易上手。价格都比较贵，[BeagleBone][5]便宜些，700，panda得2000，日，开发板还这么贵，还是量产的便宜。这里面有个输出显示的问题，触摸屏一般都是扩展卡的形式，得另外买，如果有HDMI输出则有现成的PC显示器。还有另外的方法：VNC，不过就少了触摸的乐趣。

//这里有个问答帖子[What&#8217;s the simplest Linux capable board I could make at home?][6]。从回答上看自己做个还不如买个做好的，因为ARM芯片或者AVR32（这个也可以跑Linux，不过有ARM谁会玩这个啊，虽然我有点atmel情节&#8230;）都是贴片的，焊接困难。

//Arch Linux也有跑在ARM上的[版本][7]。支持的ARM版本比较广泛，从ARMv5到Cortex-A9都可以。

//今天发现一个牛人的东东：在8位的ATmega1284p上面跑linux（非完整的UBUNTU），纯手工制作，还在AVR上做了个ARM模拟器，牛的一塌糊涂。不过速度就慢了，启动就得花2个小时。这个ATmega1284p是DIP封装的，看来ARM多半没有DIP封装的。

//国产的ARM开发板就便宜多了，一般500多还带LCD。比较有名的是友善之臂的[Tiny6410][8]（想到<del>友善的狗</del>）。感觉支持的还行，linux/android都可以，文档也很齐全，就是2D, 3D驱动没有开源。U比较老，android 4.0是没戏的。

//LeafLabs Maple &#8211; 这是跟Arduino很相像的ARM 开发板，便宜，才100多块钱，STM32F103RB芯片（这个可可能是开发板上用的最多的ARM芯片）。不过如果有了AVR版本的Arduion，要这个做啥呢？后来看了下Arduion也出来了官方的ARM版本 &#8211; Arduion Due。看了地球人不能阻挡ARM的步伐了&#8230;

//如果要搞STM32，taobao 大菠萝上面有很多开发板，大多是基于Cortex-M3，可能只是廉价的ARM方案。

//其实我想一个更好的方案是搞些便宜的android平板做开发板，可能没有PIN接口，不过对于ARM来说，我想软件才是最重要的。

纠结了一个月，最后买了beaglebone。感谢老婆，感谢社会，感谢淘宝。。。

 [1]: https://wiki.ubuntu.com/ARM
 [2]: http://baike.baidu.com/view/441609.htm
 [3]: http://www.raspberrypi.org/
 [4]: http://elinux.org/CH:R-Pi_Hub
 [5]: http://beagleboard.org/bone
 [6]: http://electronics.stackexchange.com/questions/1738/whats-the-simplest-linux-capable-board-i-could-make-at-home
 [7]: http://archlinuxarm.org/
 [8]: http://www.arm9.net/tiny6410.asp