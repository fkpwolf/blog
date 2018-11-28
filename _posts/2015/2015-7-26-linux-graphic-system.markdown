---
layout: post
title:  "Linux 图形系统？"
date:   2015-7-26 16:27:00
---

我原来的老帖子：
1. http://fkpwolf.net/2012/11/03/framebuffer-on-raspberry-pi/
2. http://fkpwolf.net/2012/12/28/light-weight-window-for-raspberry-pi/

Gnome 3 has include clutter https://wiki.gnome.org/Projects/Clutter
```
ldd /usr/bin/gnome-shell | grep -i clutter
libclutter-1.0.so.0 => /usr/lib/x86_64-linux-gnu/libclutter-1.0.so.0
```
http://www.tuxradar.com/content/clutter-beginners-tutorial It will distort screen to low screen resolution in hiDPI mode.

debug Gnome : You currently run it by pressing Alt-F2, typing lg, then Return. https://wiki.gnome.org/Projects/GnomeShell/LookingGlass

Gnome Shell use Mutter as window manager. Mutter https://en.wikipedia.org/wiki/Mutter_(software) is a Wayland compositor like Weston. Mutter use Clutter as graphics library.
A new era for Linux's low-level graphics - Part 1 https://www.collabora.com/news-and-blog/blog/2018/03/20/a-new-era-for-linux-low-level-graphics-part-1/

How to know whether Wayland or X11 is being used. <https://unix.stackexchange.com/questions/202891/how-to-know-whether-wayland-or-x11-is-being-used>

https://medium.com/@bugaevc/how-to-easily-determine-if-an-app-runs-on-xwayland-or-on-wayland-natively-8191b506ab9a

Does your application run on Wayland natively, or uses XWayland (X11 compatibility layer)? <https://fedoraproject.org/wiki/How_to_debug_Wayland_problems#Does_your_application_run_on_Wayland_natively.2C_or_uses_XWayland_.28X11_compatibility_layer.29.3F>

https://www.phoronix.com/scan.php?page=news_item&px=GTK-Vulkan-Wayladdnd-Setup

[Programming Wayland Clients](https://jan.newmarch.name/Wayland/index.html)，这本书不错，很多 hello world 的代码。
https://jan.newmarch.name/Wayland/EGL/ 编译方法 `cc -o egl egl.c -lwayland-client -lEGL -lwayland-egl`
编译好的程序如果在 text mode 下运行，会报 can't connect display 的错误，但是如何在运行完 mutter 后运行程序呢？可能[这里的方法](https://unix.stackexchange.com/questions/93752/run-mutter-window-manager-standalone)会有作用。

![wayland1](/images/2015/wayland1.png)

From <https://wayland.freedesktop.org/architecture.html>

![wayland2](/images/2015/wayland2.png)

From <https://en.wikipedia.org/wiki/Wayland_(display_server_protocol)>
为什么要单独出 KMS 呢？全部给 DRM 不好？
Wayland compositor and its clients use EGL to draw directly into the framebuffer. 既然如此，wayland就做很少事情了？但是 wayland 应该还是封装了 EGL，比如上面的 W2EGL glue code。上面的 2，3是消息。
https://en.wikipedia.org/wiki/Evdev Linux kernel → libevdev → libinput → Weston → Wayland client, xorg 也用了 libevdev，所以输入子系统这块基本是相同的。
```
[fan@intelway ~]$ lsof /usr/lib/libevdev.so
COMMAND    PID USER  FD   TYPE DEVICE SIZE/OFF    NODE NAME
gnome-she 1205  fan mem    REG    8,1   105520 3439743 /usr/lib/libevdev.so.2.1.21
```

DRM https://en.wikipedia.org/wiki/Direct_Rendering_Manager 允许多个程序同时使用 GPU。DRM 获得对 Video Card 的独占访问权限，它负责初始化和维护命令队列、Video RAM 以及其他相关的硬件资源。https://book.iyounix.com/device-porting/drm/linux-drm-1-display-server-history.html

DRI https://en.wikipedia.org/wiki/Direct_Rendering_Infrastructure 似乎只有 X 才会使用

https://en.wikipedia.org/wiki/Framebuffer  这个是显卡的设备

https://en.wikipedia.org/wiki/Mesa_(computer_graphics) 开源的显卡驱动，是OpenGL, ES, EGL, Vulkan的软件实现。运行 glxinfo 可以得到：
OpenGL vendor string: Intel Open Source Technology Center
OpenGL renderer string: Mesa DRI Intel(R) Sandybridge Server
EGL 是 OpenGL ES 渲染 API 和本地窗口系统(native platform window system)之间的一个[中间接口层](https://woshijpf.github.io/android/2017/09/04/Android系统图形栈OpenGLES和EGL介绍.html)，通过调用 eglCreateWindowSurface 提供给 OpenGL 一个 surface / 画布，还做 front buffer 和 back buffer 之间的切换 eglSwapBuffer。如此看来 EGL 做了相当多的事情了，而 wayland 只是处理了鼠标键盘事件？

https://en.wikipedia.org/wiki/Compositing_window_manager A compositing window manager, or compositor, is a window manager that provides applications with an off-screen buffer for each window. The window manager composites the window buffers into an image representing the screen and writes the result into the display memory. 对于 EGL 部分，就交给 libGL 了。https://en.wikipedia.org/wiki/Mutter_(software) Gnome3 默认的管理器。
总的来说，compositor 就是支持多窗口界面了。Now there is no X daemon running. No C-S stuff. But how to make sure Mutter running/working? "lsof | grep mutter" say mutter used by gnome-shell as library. So just directly run gnome-shell (if installed GDM, GDM will start gnome-shell)? Even the libwayland-server is a library! All direct call, no message? input event is still sent by IPC as C-S style as X, but rendering is directly: application directly call EGL.
There is command 'mutter'. Why it exit after Gnome startup? In text mode, can run 'mutter --wayland'. Just a blank window. To enter text mode, first temporary stop GDM, then ctl+alt+f2.

![wayland3](/images/2015/wayland3.png)

这个IPC应该需要有个常驻的server进程吧？还是全部是callback钩子？

```
On Ubuntu 17.10,
fan@fan-APLD-MINI:~$ dpkg-query -L libmutter-1-0
/.
/usr
/usr/lib
/usr/lib/x86_64-linux-gnu
/usr/lib/x86_64-linux-gnu/libmutter-1.so.0.0.0
/usr/lib/x86_64-linux-gnu/mutter
/usr/lib/x86_64-linux-gnu/mutter/libmutter-clutter-1.so
/usr/lib/x86_64-linux-gnu/mutter/libmutter-cogl-1.so
/usr/lib/x86_64-linux-gnu/mutter/libmutter-cogl-pango-1.so
/usr/lib/x86_64-linux-gnu/mutter/libmutter-cogl-path-1.so
/usr/share
/usr/share/doc
/usr/share/doc/libmutter-1-0
/usr/share/doc/libmutter-1-0/changelog.Debian.gz
/usr/share/doc/libmutter-1-0/copyright
/usr/lib/x86_64-linux-gnu/libmutter-1.so.0
fan@fan-APLD-MINI:~$ lsof /usr/lib/x86_64-linux-gnu/libmutter-1.so.0
lsof: WARNING: can't stat() tracefs file system /sys/kernel/debug/tracing
  Output information may be incomplete.
COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME
gnome-she 1092 fan mem REG 179,2 1366928 1182432 /usr/lib/x86_64-linux-gnu/libmutter-1.so.0.0.0
GNOME Shell is written in C and JavaScript as a plugin for Mutter.
fan@fan-APLD-MINI:~$ apt-cache search libwayland
libwayland-bin - wayland compositor infrastructure - binary utilities
libwayland-client0 - wayland compositor infrastructure - client library
libwayland-cursor0 - wayland compositor infrastructure - cursor library
libwayland-dev - wayland compositor infrastructure - development files
libwayland-doc - wayland compositor infrastructure - documentation files
libwayland-egl1-mesa - implementation of the Wayland EGL platform -- runtime
libwayland-server0 - wayland compositor infrastructure - server library
fan@fan-APLD-MINI:~$ apt-cache search libgl|grep mesa
libgl1-mesa-dev - free implementation of the OpenGL API -- GLX development files
libgl1-mesa-dri - free implementation of the OpenGL API -- DRI modules
libgl1-mesa-glx - free implementation of the OpenGL API -- GLX runtime
libglapi-mesa - free implementation of the GL API -- shared library
libgles2-mesa - free implementation of the OpenGL|ES 2.x API -- runtime
libgles2-mesa-dev - free implementation of the OpenGL|ES 2.x API -- development files
libglu1-mesa - Mesa OpenGL utility library (GLU)
libglu1-mesa-dev - Mesa OpenGL utility library -- development files
libglw1-mesa - GL widget library for Athena and Motif -- runtime
libglw1-mesa-dev - GL widget library for Athena and Motif -- development files
```

![wayland4](/images/2015/wayland4.png)

有了 DRM 之后，x client 可以直接访问 drm，这样一来 x windows 似乎和 wayland 差不多了。

![wayland5](/images/2015/wayland5.png)

这个对比更为清楚。主要是对于普通 X11 application，所有的渲染操作都是 server 完成，client 只发送消息，这属于典型 C/S 模式，性能会有额外开销。Wayland 则是自己直接渲染。

### 树莓派
https://github.com/anholt/mesa/wiki/VC4 Enabling the driver is controlled using raspi-config. Select Advanced Options option and then GL Driver.
Raspbian only supports vc4 on the Raspberry PI 2 and later. The driver supports Raspberry Pi 1, but raspi-config refuses to do it.
https://www.collabora.com/news-and-blog/blog/2016/06/03/running-weston-on-a-raspbian/ 只有少量程序可以运行


Evolution in Flatpak https://wiki.gnome.org/Apps/Evolution/Flatpak
As of Evolution release 3.30.0, users can build the latest stable (or development) version of Evolution using Flatpak.As of Evolution release 3.30.0, users can build the latest stable (or development) version of Evolution using Flatpak.
沙箱运行环境，桌面这么大的也可以运行在沙箱里面？用来做开发测试倒是蛮好的。