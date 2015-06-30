---
title: 使用QEMU运行Windows
author: Fan Fan
layout: post
permalink: /2013/11/10/run-windows-on-qemu/
aktt_notify_twitter:
  - no
categories:
  - UNIX
---
事情起因是最近升级Windows，结果发现Windwos 8.1对USB驱动有改变，rockchip的刷机usb口不能用了。

试过vmware player，结果安装需要/etc/init.d，而archlinux没有这个。virtual box没办法启用usb（pass through），据说社区版只能这样云云。总之这些商业版本感觉很庞大。

用了下qemu，原来以为学习、研究的玩具，结果比想象中的好用：小巧快速。

1）启动速度很快，没有bios画面。可能是使用了kvm的原因。界面很简洁，因为界面只有命令行的。使用“-smp 2 -cpu host”后，windows cpu评分为7（GPU只有2）。

2）可以使用Host的usb设备（pass through）。比如&#8221;-usbdevice host:2207:300a&#8221;。对于usb权限问题，新建文件/etc/udev/rules.d/33-mk808b.rules：

ACTION==&#8221;add&#8221;, SUBSYSTEM==&#8221;usb&#8221;,ATTR{idVendor}==&#8221;2207&#8243;, OWNER=&#8221;fan&#8221;

现在连GPU也可以[穿透][1]了（无视目标护甲orz），这个太强了，以后再插块显卡就可以在linux下玩游戏了。

3）加了&#8221;-usbdevice tablet&#8221;后，鼠标就可以无缝切换了。

4）windows xp太旧，没网卡驱动。改用windows 8，相对7对系统资源要求更低。

5）clipboard还有samba现在还没有办法使用。

qemu-system-x86_64 &#8211;enable-kvm qemu-img -m 4096 -usbdevice tablet  -usbdevice host:2207:300a -smp 2 -cpu host

[<img class="alignnone size-large wp-image-1480" title="Untitled" src="http://fkpwolf.net/WordPress/wp-content/uploads/2013/11/Untitled-1024x920.png" alt="" width="580" height="521" />][2]

 [1]: http://www.phoronix.com/scan.php?page=news_item&px=MTUwOTc
 [2]: http://fkpwolf.net/WordPress/wp-content/uploads/2013/11/Untitled.png