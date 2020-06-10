---
title: Light weight Window For Raspberry Pi
author: Fan Fan
layout: post
permalink: /2012/12/28/light-weight-window-for-raspberry-pi/
aktt_notify_twitter:
  - no
categories:
  - Graph
---
想模仿Android，后者是用了skia的框架（directFB么？）。我想Chromium OS也是用同样的东西把。后者已经可以在RPi上面运行了，不过从[这里][1]看好像还是运行在x-windows上面。

那就只能用wayland了，这个是完整的窗口系统。光有framebuffer driver是不够的，后面的kde或者gnome都跟不上来。

在RP上面编译Wayland的看[这里][2]。一步一步来居然没有编译错误。注意每次运行weston的时候需要export变量。起来后窗口界面只有一个terminal，<del>没有其他程序</del>（在weston/clients下面有些小程序，比如flower、image），也不能chromium，有点失望。如果想要运行更多的窗口，那得编译以wayland为backend的Qt5或者GTK 3。不过后者都太庞大，能流畅运行么？（窗口界面而非图形特效）

于是想跑跑wayland下的Clutter，按照这里的[指南][3]，在编译cogl没能过去。我的参数是“./autogen.sh &#8211;prefix=$WLD &#8211;enable-wayland-egl-server=yes &#8211;enable-gles2=yes &#8211;enable-cogl-gles2=yes &#8211;enable-gl=no &#8211;disable-x11-compositor &#8211;disable-glx”，但是没有去掉对x11的依赖。

[新版的][4]直接运行apt-get install weston就可以，然后运行weston-launch既可，不需要设置环境变量。

现在RPi有更为成熟的wayland port：Maynard，因为现在VideoCore号称是ARM里面唯一开源GPU，所以动画效果运行起来也是很流畅。
不知道Ubuntu下面wayland的竞争者Mir能不能移植到RPi上面。

[flutter-pi][5] 直接在RPi上面运行 Flutter，并不依赖 X11，只要支持 DRI 就可以了。看上去很有趣。

[Raspberry Pi 4 Vulkan Source Code Released, More Complex Vulkan Demos Supported][6] 新的驱动

[GNOME Renders on Arm Mali-G31 Bifrost GPU with Fully Open Source Code][7] 在 ODROID Go Advance 上面运行完整的 Wanland Gnome 3。

 [1]: http://www.raspberrypi.org/phpBB3/viewtopic.php?p=201360#p201360
 [2]: http://wayland.freedesktop.org/raspberrypi.html
 [3]: http://wayland.freedesktop.org/clutter.html
 [4]: http://fooishbar.org/tell-me-about/wayland-on-raspberry-pi/
 [5]: https://github.com/ardera/flutter-pi
 [6]: https://www.cnx-software.com/2020/06/10/raspberry-pi-4-vulkan-source-code-released-more-complex-vulkan-demos-supported/
 [7]: https://www.cnx-software.com/2020/06/09/gnome-renders-on-arm-mali-g31-bifrost-gpu-with-fully-open-source-code/
