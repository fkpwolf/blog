---
title: 在Mac OS X上编译Clutter
author: Fan Fan
layout: post
permalink: /2010/11/01/compile-clutter-on-mac-osx/
dsq_thread_id:
  - 605356862
categories:
  - Graph
---
在MacPorts上已经有Clutter，但是编译和链接tests里面的c文件时报错，说是找不到_main的定义。定义在clutter-main.c里面，但是不知道为什么port编译后的库里面找不到这个引用。

于是自己编译。其实参考port的[定义][1]就很容易了：

./configure &#8211;with-flavour=osx &#8211;enable-introspection=no &#8211;disable-gtk-doc

这里和port不同的是用的是osx，而port用的是glx。然后make就没有错误了，可能刚开始运行port install clutter时把一些依赖的库比如cairo, mesa都安装了，这次才会这么顺利。

照提示的信息看，osx的这种beckend现在还是实验性的，尝试运行了clutter/tests/interactive，有的有问题，有的没有问题。下面是test-cairo-flowers的截图：(后来发现[官方文档][2]对这已经有描述了。。。)

[<img class="alignnone size-full wp-image-842" title="clutter" src="http://fkpwolf.net/WordPress/wp-content/uploads/2010/11/clutter.png" alt="" width="640" height="502" />][3]

最新For Lion: [这里][4]有有份官方文档，看来这还真是混乱。现在configurate的时候会出现新的问题：

Requested &#8216;cogl-1.0 >= 1.9.6&#8242; but version of COGL is 1.6.20

cogl是clutter自身带的，居然没有找到。我没有办法，只能把这些依赖从configurate里面删除。但是make时候出现错误“error: cogl/cogl.h: No such file or directory”，看来cogl不是clutter自带的，于是我试了下

<pre>git clone git://git.gnome.org/cogl</pre>

果然有这个项目，日啊。编译时用“./configure &#8211;prefix=/opt/local &#8211;disable-introspection”，否则又会有glib的错误。sudo make install后再回到clutter下面重新configure，果然cogl的错误信息没有了。

接下来在make clutter的时候，又出现错误：

&#8216;trackingRect&#8217; undeclared (first use in this function)

这个好像是objective-c代码上的问题，得修改代码么？痛苦啊。看了下，将就改成下面的。

<pre class="brush:c">- (NSTrackingRectTag) trackingRect
{
  return tracking_rect;
}

- (void) dealloc
{
  if (tracking_rect)
    {
      [self removeTrackingRect:tracking_rect];
      tracking_rect = 0;
    }

  [super dealloc];
}

/*
- (void) dealloc
{
  if (trackingRect)
    {
      [self removeTrackingRect:trackingRect];
      trackingRect = 0;
    }

  [super dealloc];
}
*/</pre>

make;make install现在都没有问题。不过在运行test case时出现问题，跳出一个X11的窗口，奇怪啊。

 [1]: http://trac.macports.org/browser/trunk/dports/graphics/clutter/Portfile
 [2]: http://docs.clutter-project.org/docs/clutter/1.3/building-clutter.html
 [3]: http://fkpwolf.net/WordPress/wp-content/uploads/2010/11/clutter.png
 [4]: http://wiki.clutter-project.org/wiki/BuildingClutterOnOSX