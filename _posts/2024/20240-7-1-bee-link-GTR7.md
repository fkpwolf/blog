---
layout: post
title: "零刻 GTR7 & mini 小主机"
date: 2021-8-8
categories:
  - DIY
typora-root-url: ../../../blog
---

# 零刻 GTR7 & mini 小主机

Created: June 10, 2023 10:04 AM
Tags: review

### 链接

[https://www.bee-link.com.cn/catalog/product/index?id=808](https://www.bee-link.com.cn/catalog/product/index?id=808) Product page.

[https://www.bee-link.com.cn/cms/support/driverhardware](https://www.bee-link.com.cn/cms/support/driverhardware) Driver download.

[https://www.wolai.com/meiss/76fqibfv2v7iN8wAnfWLZ4](https://www.wolai.com/meiss/76fqibfv2v7iN8wAnfWLZ4) BIOS upgrade guide.

[https://weibo.com/u/7319305762](https://weibo.com/u/7319305762) 官方微博

### 体验

背部接口没有标记，雷电接口也没有。

前面板非雷电TYPE-C 10GB，没有20GB的。后面雷电接口接雷电硬盘盒可以跑满速，但是接20gb硬盘盒只能跑10gb。

DDR5能到5600，非常不错。这个对于核显的发挥很重要。

上面板进风，Logo有遮挡。虽然有风格，但是散热是第一位的。HWiNFO 里面看不到风扇转速。

如果顶部完全封闭，那就可以放其他设备了，这样更适合放到桌面上。放到桌面上后USB接口就容易拔插。铭凡UM780 XTX（126x130x60），零刻SER8&SEi14（135x135x44.7），机械革命imini Pro820（154x152.2x37.8），蝰蛇峡谷（230x180x60）都是这种类型。这么说mac mini（197x197x35.8）之所以还是原来的老尺寸，容易堆叠是重要原因。

内部很精细，达到了NUC水平。

BIOS很老土，但是可以改进，问题也不大。不得非要图形化BIOS，功能可用即可。

CLR CMOS，这个按钮为什么放前面？

指纹不错，Windows Hello可以用。容易触碰，导致关机。Linux 下对这个指纹芯片还不支持。[https://linux-hardware.org/?id=usb:1c7a-0577](https://linux-hardware.org/?id=usb:1c7a-0577)

外观色彩里面应该有个黑色。或者只有黑色。

体积还是大了点。

Windows 下面机器没法休眠，BIOS这个可以改进。my question [https://tieba.baidu.com/p/8604777597](https://tieba.baidu.com/p/8604777597)。Linux可以。

AMD贴纸在机器反面，好评。

磁吸电源接口，拔插手感一般，没有苹果笔记本的顺畅。不过这个没带电池，拔掉就关机，平时也不像笔记本那样经常拔插，手感不好问题不大。但有这个创新意识就很好。贴吧认为联想拯救者C130和C140电源兼容性比较好，支持20Vx6A功率。雷电4电源，可以开机，但是过一会就自动关机了。客服说是开机峰值110W，PD最高只能100W。感觉这个峰值可以在BIOS里面微调，还是等待以后改进吧。其实这个使用场景更多是为笔记本服务的（反向充电），mini小主机并不会从中受益很多，主要是要占用一个雷电接口。总的来说，PD电源有不少兼容性问题，而这个对于主机电源来说是致命的。

贴吧很多（[这里](https://tieba.baidu.com/p/8506044648)，[这里](https://tieba.baidu.com/p/8530868595)）反映重启问题，在双条内存下，播放视频会重启。似乎是和显卡驱动有关系。我内存是棘蛇JAZER 8GB DDR5 5600，CPU-Z显示为海力士颗粒。

### 雷电接口

雷电显示器开始没法显示开机BIOS画面，要等到进入windows后才会点亮屏幕。但是新款BIOS解决了部分问题：可以在type-c显示器上面操作。虽然开机时间边长了，但是这个改进的速度还是很让人点赞的。

最早的显卡驱动版本，工作正常。升级成官方新版本后无法驱动雷电显示器，只能回退。后来从成功升级到2023/5/30，原因可能是需要安装 chipshet 驱动，或者安装驱动时选择 minimal+reset config。最好接上 HDMI 再安装，否则中途会出错。

默认设置进入linux，之后如果要重启进入windows，可以使用 efibootmgr 命令。

Linux下面待机后恢复很快，但是待机后如果雷电hub接过其他设备然后再切换回来，恢复有点慢，但最后还是能成功。

[teardown](https://www.zhihu.com/tardis/zm/art/636603672?source_id=1003), looks no USB4.0 chip. Supported by 7840HS natively?

```jsx
00:03.1 PCI bridge: Advanced Micro Devices, Inc. [AMD] Family 19h USB4/Thunderbolt PCIe tunnel
00:04.1 PCI bridge: Advanced Micro Devices, Inc. [AMD] Family 19h USB4/Thunderbolt PCIe tunnel
ca:00.5 USB controller: Advanced Micro Devices, Inc. [AMD] Pink Sardine USB4/Thunderbolt NHI controller #1
ca:00.6 USB controller: Advanced Micro Devices, Inc. [AMD] Pink Sardine USB4/Thunderbolt NHI controller #2
```

这个机器能驱动 LG Ultrafine 5K，是因为雷电有两条 DP OUT 么？如何在 Windows 下面查看？

![Untitled](%E9%9B%B6%E5%88%BB%20GTR7%20&%20mini%20%E5%B0%8F%E4%B8%BB%E6%9C%BA%202b09c1e836614cc3bcbf315ab677cffc/Untitled.png)

确实有两个 DP IN，如果接普通4K显示器，上面隧道DP IN为1。而 780M 显卡输出为4 Displays (max.), HDMI 2.1, DisplayPort 2.1 [https://www.notebookcheck.net/AMD-Radeon-780M-GPU-Benchmarks-and-Specs.680539.0.html](https://www.notebookcheck.net/AMD-Radeon-780M-GPU-Benchmarks-and-Specs.680539.0.html) 所以这是全接出了。

### 散热

![Untitled](%E9%9B%B6%E5%88%BB%20GTR7%20&%20mini%20%E5%B0%8F%E4%B8%BB%E6%9C%BA%202b09c1e836614cc3bcbf315ab677cffc/Untitled%201.png)

拆机[视频](https://post.smzdm.com/talk/p/a305x3m7/)。从面板的后部排风口看，涡轮风扇距离面板很近，所以并不能靠两侧的孔网进风。但是因为是涡轮风扇，感觉上面板盖住问题也不大，只要不是全封闭。

### Change boot order

[https://superuser.com/questions/1338643/how-do-i-change-the-uefi-boot-order-from-within-windows-10](https://superuser.com/questions/1338643/how-do-i-change-the-uefi-boot-order-from-within-windows-10)

[https://superuser.com/questions/1016762/is-it-possible-to-select-which-system-to-boot-before-rebooting-on-a-multi-boot](https://superuser.com/questions/1016762/is-it-possible-to-select-which-system-to-boot-before-rebooting-on-a-multi-boot)

Set Linux as default boot OS. In Linux, run command `sudo efibootmgr -n 0000` to set EFI BootNext var which will boot to Windows only next time. The boot order is not changed.

### 铭凡和其他品牌

[https://www.sohu.com/a/713846019_121649107](https://www.sohu.com/a/713846019_121649107)

BD790i，7945HX，TYPE-C显示，无雷电。这个级别的CPU还是适合作为游戏笔记本，因为iGPU羸弱，加个独立显卡刚好。mini小主机加显卡就不能mini了，必须要有专门定制的机箱，类似NUC幽灵峡谷，否则和自己配ITX主板+显卡没啥优势。“银欣 ML12薄型ITX小机箱”这个还可以，因为这个mini主机散热只能用下压了。或者分形工艺 Ridge。[这里](https://www.chiphell.com/thread-2597430-1-1.html)看感觉小厂实力。[这里](https://minisforum.hk/products/bd770i)装机展示用的机箱是酷冷至尊NR200，评论里面有用XPROTO-Mini这种全开放式机箱。

UM880PRO，2599￥，8845HS，双USB4，改进后的型号。

HX100G: 7840HS+RX6650M，双雷电口，这样可惜集成显卡了。HX200G：7945HX+7600M XT，这个配置很梦幻，但是雷电口就没有了，TYPE-C也没有标明是否有显示输出。原子侠 G7，7945HX+7600M XT。

AR900i(板载i9-13900HX)，也没有雷电接口，但是有type-c显示输出。

玄派创世魔方，8845HS，原生oculink，双雷电，图形化BIOS。堪称ai pc前的辉煌了。B站上有不少评测。就是外观设计有点呆，底部进风也很小。

[2024玲珑产品发布会——世界上首台可以折叠的键盘迷你主机](https://www.notion.so/GTR7-mini-2b09c1e836614cc3bcbf315ab677cffc?pvs=21) 我觉得还是很有想法的，就是略微尴尬，不知道用在哪里。屏幕演示里面主要是ipad，我没有这个习惯。用ipad是讨巧的场景，如果用便携屏幕就真不如笔记本了。

### MS-01 和其他全闪NAS小主机

[https://minisforum.hk/products/ms01](https://minisforum.hk/products/ms01)，[https://www.chiphell.com/thread-2542008-1-1.html](https://www.chiphell.com/thread-2542008-1-1.html) 讨论贴，[https://www.chiphell.com/thread-2571531-1-1.html](https://www.chiphell.com/thread-2571531-1-1.html) 评测，[https://www.koolcenter.com/posts/465](https://www.koolcenter.com/posts/465) 评测，i5-12600H 版本 2999￥，我认为只有这个最便宜的版本才有价值，因为这个板子主要是扩展性，CPU 太高反而散热着急。[绿联DXP480T Plus](https://www.ugnas.com/products-detail/id-33.html)，和这个很像，价格也类似。DXP6800 Pro也可以，如果有机械硬盘的需求，[CHH讨论贴](https://www.chiphell.com/forum.php?mod=redirect&goto=findpost&ptid=2607738&pid=55008203)。

这种NAS的雷电能作为非host接入USB4 hub么？如果可以，这个雷电倒是有了个很好的使用场景。

### MoDT

畅网 7840HS NAS 软路由 [https://www.changwang.com/product.html?id=49](https://www.changwang.com/product.html?id=49) 带一个雷电口。[厂商介绍视频](https://www.notion.so/GTR7-mini-2b09c1e836614cc3bcbf315ab677cffc?pvs=21)，下面都是批评的，我看只是因为价格贵。感觉国内的厂商也开始有创造力了。这个和铭凡MS-01很相像了，而畅网的优势在于这个是itx主板，24pin atx电源接口，折腾的空间会更大。价格3K，没有万兆，导致那么多2.5g有点尴尬。

尔英板载CPU套装13代，一个雷电口，DDR5，13600H 1400￥，三个m.2。CPU可能比7480差，但是7840现在主板很少。MS-01其实不错，但是不是ATX电源接口灵活性不够。

### Intel Ultra

机械革命（MECHREVO）imini Pro游戏商务电脑台式迷你主机 Ultra 5 125H，只有一个雷电口。[拆解](https://post.smzdm.com/p/apml0ke7/) 另外一个型号是“Ultra 7 155h”

Asus NUC14 Pro+，双雷电口，这个型号更小，改进了进风，4299￥，贵了不少，[拆解评测](https://www.chiphell.com/thread-2611878-1-1.html)，散热很独特，涡轮风扇能两边出风。精致商用机。

Minisforum UH125 Pro 双USB4+OCulink+双5G网口，OCulink不会占用m.2。[https://minisforum.hk/products/minisforum-uh125-pro](https://minisforum.hk/products/minisforum-uh125-pro)

零刻 GTi14，内置PCIe X8插槽，感觉这样还不如像幽灵峡谷那样直接做成个nuc的主机，内置PCIe X8插槽。

### Ryzen AI 300

这个短时间内上 mini pc 有点难。

### 功耗

[https://www.cnx-software.com/2024/04/29/raspberry-pi-5-intel-n100-mini-pc-comparison-features-benchmarks-price/](https://www.cnx-software.com/2024/04/29/raspberry-pi-5-intel-n100-mini-pc-comparison-features-benchmarks-price/) “N100 mini pc to 1.3-1.5W idle power consumption”, “enabling ASPM for all PCI-E root ports and setting maximum package C-state to C10”

### thunderbolt 3 hub lost after suspend

```bash
xhci_hcd 0000:6a:00.0: xHCI host controller not responding, assume dead
6a:00.0 USB controller: Fresco Logic FL1100 USB 3.0 Host Controller (rev 10)
```

冷启动没有问题，但是suspend然后resume后就出现问题。像是这个设备的linux driver问题，同样雷电hub在b650m/b660m主机上面工作正常。感觉还是BIOS电源设置问题。这个USB HUB是pcie的，雷电hub上面的pcie网卡倒是正常工作。和BIOS里面的ASPM有关么？

on B650M

```bash
[    2.690958] xhci_hcd 0000:0c:00.0: xHCI Host Controller
[    2.690981] xhci_hcd 0000:0c:00.0: new USB bus registered, assigned bus number 1
[    2.692350] xhci_hcd 0000:0c:00.0: hcc params 0x200071e9 hci version 0x100 quirks 0x0000000000000410
[    2.692765] xhci_hcd 0000:0c:00.0: xHCI Host Controller
[    2.692788] xhci_hcd 0000:0c:00.0: new USB bus registered, assigned bus number 2
[    2.692789] xhci_hcd 0000:0c:00.0: Host supports USB 3.0 SuperSpeed
```

when resume, only `[  392.346717] xhci_hcd 0000:0c:00.0: xHC error in resume, USBSTS 0x411, Reinit`.