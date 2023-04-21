---
title: Framebuffer on Raspberry Pi
author: Fan Fan
layout: post
permalink: /2012/11/03/framebuffer-on-raspberry-pi/
aktt_notify_twitter:
  - no
categories:
  - 电子制作
---
要直接看Framebuffer运行的例子，[这里][1]有个。编译后，在字符界面的控制台下运行，可以在屏幕上看到一个红色的方块。这个时候屏幕没有切换显示模式或者分辨率，感觉和x86下的文字/图形界面切换大不一样。

如果要运行OpenGL这种更有意义的图形程序，还需要DirectFB。这个是建立在Framebuffer之上的，但是需要设备的驱动支持。[这里][2]提供了一个直接在RPi上运行DirectFB的例子，按照其[README][3]一步一步来，安装很容易。不过我在Raspbmc上insmod fusion.ko时，出现“Error: could not insert module fusion.ko: Invalid module format”的错误。更多脱离xwindows运行opengl ES的例子可以看[这里][4]。

Raspbmc是否运行在DirectFB上呢？从[这个帖子][5]看，好像是直接运行在Framebuffer上的。[其他][6]有MPlayer，GTK+和Qt/Embedded。可以避免x-windows的开销。而X-windows一般需要运行在/dev/fb0上面，也可以用自己的驱动。

有个叫OUYA的 Android 游戏盒子，为什么不直接在FrameBuffer上弄，这样性能不更好么？游戏性能应该由GPU来决定，FrameBuffer只是一个对硬件和驱动的封装。

看[Firefox OS的架构][7]，也是在framebuffer上面运行opengl es。

文本模式下如果要显示企鹅图标的话，就必须用到FrameBuffer。

[LVGL][8] 可以直接运行在Framebuffer上，在小型设备上面提供轻量级的图形库。这个Framebuffer可以由硬件或者软件提供，最终让程序通过操作内存来刷新显示。在Linux系统里面，这个是标准设备 `/dev/fb0`。

 [1]: http://www.raspberrypi.org/phpBB3/viewtopic.php?p=169305#p169305
 [2]: https://plus.google.com/109054407220184149542/posts/PGEHcHazRow
 [3]: http://directfb.org/raspbian/README
 [4]: http://benosteen.wordpress.com/2012/04/27/using-opengl-es-2-0-on-the-raspberry-pi-without-x-windows/
 [5]: http://forum.xbmc.org/showthread.php?tid=133722
 [6]: http://en.wikipedia.org/wiki/Linux_framebuffer
 [7]: https://developer.mozilla.org/en-US/Firefox_OS/Platform/Architecture
 [8]: https://github.com/lvgl/lvgl
