---
layout: post
title: "技嘉 B650E USB4 评测"
date: 2025-6-1
categories:
  - DIY
typora-root-url: ../../../blog
---

[B650E AORUS PRO X USB4 leak](https://unikoshardware.com/2024/06/gigabyte-b650e-aorus-and-x870e-leak.html)，[拆解报告](https://post.smzdm.com/p/am3px0zz/)。Hub 连在 CPU 上面据说很少见，记录下：`USB2.0 hub, GL850G. USB3.2 hub, RTS5411S`. 这样作好处是更多USB接口？确实和下面[规格](https://www.aorus.com/zh-tw/motherboards/b650e-aorus-pro-x-usb4-rev-10/Specification)里面的一样。

![block](/images/2025/b650e-usb4.png)

USB4如果挂在PCH下面那这个ATX板子就没有 pcie x4 插槽了。总体来说和自己配的B650m pci切分没啥区别，因为pcie lane总数就那么点。b650e 支持pcie x16 5.0，b650只支持4.0，但是这个对于我来说意义不大。[CHH讨论贴](https://www.chiphell.com/thread-2618079-1-3.html)。主要是没有DP IN，这个主板如果接独立显卡就没啥必要了。如果matx/itx板子有usb4，只用集成显卡，似乎是个不错的开发机。挂CPU上面主要原因：
1. 南桥只有pcie x4带宽
2. 对于CPU DP OUT走线方便

从[泄露](https://www.4gamers.com.tw/news/detail/64969/gigabyte-x870e-aorus-master-ppt-leaks-amd-ryzen-9000-spec)的x870e aorus master看，它的USB4似乎是挂在PCH上面（不清楚），但是可以确定的是没有DP IN，这样它会多一个PCIE 5.0 M.2，但是这个对于我来说并不重要。

![Untitled](/images/2025/b650e-usb4-control-pannel.png)
5K正常，两个DP IN，加上后置面板的HDMI和板载HDMI，所有接口引出了。两个USB4口如果先接LG 5K，再接便携显示器，便携显示器没有信号，因为总共才两个DP IN。如果先接便携显示器，再5K，都有信号，但是5K显示器分辨率不够，因为这个LG 5K显示器需要两个DP。

![Untitled](/images/2025/gtr7-usb4-control-pannel.png)
上面是GTR7，有两个USB4 route，能同时驱动5k和4k便携屏幕。我的thinkpad p14s也是这样。这个GTR7有四个DP IN？但是后面接口还有DP+HDMI输出啊。

[技嘉官网](https://www.gigabyte.com/Motherboard/B650E-AORUS-PRO-X-USB4-rev-10)没有雷电驱动和工具。BIOS里面没有类似原来Titan Ridge的配置菜单，可能后续更新会开放吧。主板带雷电 header，这个在这代主板里面算罕见了，加上支持 PCIe 拆分，可以称B650最后的经典。

Windows 进去后一切正常。待机后恢复正常，可以用键盘鼠标唤醒。MT7925没有驱动，需要从技嘉官网安装。“UCM-UCSI ACPI Device” 设备无法启动，看上去是“ACPI\ITE8800 ITE USB Connector Client Device”. At Linux, I saw a device “Integrated Technology Express, Inc. RGB LED Controller”.

Linux 进入登录界面无法使用键盘鼠标，`ip addr`里面没有雷电hub的网卡。ASM4242[官网](https://www.asmedia.com.tw/product/e20zx49yU0SZBUH5/363Zx80yu6sY3XH2)说“Support inbox driver of Microsoft Windows 11 & various Linux kernels.” Just manually authorize Thunderbolt device then OK. 待机后可以用键盘鼠标唤醒，但是显示器没有输出，这个问题和Asus USB4 Card一样。
