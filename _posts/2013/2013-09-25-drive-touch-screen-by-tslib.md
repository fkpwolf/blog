---
title: 使用tslib驱动U30gt的触摸屏
author: Fan Fan
layout: post
permalink: /2013/09/25/drive-touch-screen-by-tslib/
aktt_notify_twitter:
  - no
categories:
  - 电子制作
---
1.  测试触摸屏是否可用。参考<a id="" href="https://wiki.archlinux.org/index.php/Touchscreen" shape="rect" target="_blank">https://wiki.archlinux.org/index.php/Touchscreen</a>其中的命令。如果成功，则表明kernel中的驱动可用，接下来，还要让其在window server中也可用，这就需要另外一个X下面的驱动。

2. 安装*xserver-xorg-input-tslib*和*libts-bin*这两个package。运行“evtest /dev/input/event1”，你会看到很多类似ABS\_MT\_POSITION\_X这样的事件。MT表示multi-touch。但是当前的tslib并不支持多点触摸，所以要把多点触摸的事件转换为单点的。修改很简单，只是个简单的替换，比如前面的消息需要替换成ABS\_X。还要改的是ABS\_MT\_POSITION\_Y和ABS\_MT_PRESSURE。改好后用*ts_test*[测试][1]，确保这个可以过。有这个小工具就会事半功倍。

[<img class="alignnone size-medium wp-image-1468" title="IMG_20130920_095609" src="http://fkpwolf.net/WordPress/wp-content/uploads/2013/09/IMG_20130920_095609-225x300.jpg" alt="" width="225" height="300" />][2]

3. 运行xfce，如果触摸屏不能用，那么还要修改xserver-xorg-input-tslib这个驱动。打开xf86-input-tslib/src/tslib.c，去掉其中的对xf86XInputSetScreen调用。

4. 这两个package的修改可以用“*apt-get build-dep xxx;apt-get source xxx; make; make install*”方式。

5. evdev其实[已经支持][3]多点触摸，那为什么默认的配置不能用呢？可能有些兼容性的问题。另外，我尝试修改ft5406的驱动，关掉multi-touch的标志，这样就可以产生ABS\_X和ABS\_Y。但是奇怪的是没有ABS_PRESSURE。

 [1]: http://boundarydevices.com/debian-in-more-depth-adding-touch-support/
 [2]: http://fkpwolf.net/WordPress/wp-content/uploads/2013/09/IMG_20130920_095609.jpg
 [3]: http://www.phoronix.com/scan.php?page=news_item&px=MTA2ODE