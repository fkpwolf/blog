---
title: 玩玩Raspberry Pi
author: Fan Fan
layout: post
permalink: /2012/10/28/play-raspberry-pi/
aktt_notify_twitter:
  - no
categories:
  - 电子制作
---
从英国到上海，花了三个月才送到我家，等的都快忘记了。

用xbian连上电视机的样子，高清播放没有问题，很流畅。无线网卡有些问题，如果驱动上，进入图形界面后，罗技的无线键盘和鼠标会失去响应。但是用官方的标准系统没有这个问题，所以看来是xiban或者是xmbc的问题。上面图片中我用的是一个闲置的路由器作为桥接AP给这个小设备提供网络。第一次使用桥接功能，感觉还是有点神奇的。

Raspberry Pi比起其他的开发板优点在于便宜，而且提供HDMI接口、DSI和CSI接口（这个分别可以连上小型显示设备和摄像头），对于不懂如何把beaglebone连上显示器的菜鸟来说，可玩性极高。我看电路板，走线比beaglebone简单很多，难道beaglebone提供个hdmi接口就有这么难么？搞不懂。

xbian是基于xbmc的，玩了会，发现这个东西UI还是很专业的，比电视机菜单高级多了，IPTV相比下那是非常的土。而且为了保证性能，没有基于[x-windows][1]，直接运行在[framebuffer][2]上，还可以这样玩？！

据说这个设备USB电源设计有问题，我试了下，确实有。比如启动系统时，插入USB无线网卡，RPi会自动重启。后来入了个带外部电源的USB Hub，这个问题解决。下面是lsusb的结果：

<pre>Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 001 Device 002: ID 0424:9512 Standard Microsystems Corp.
Bus 001 Device 003: ID 0424:ec00 Standard Microsystems Corp.
Bus 001 Device 004: ID 1a40:0101 Terminus Technology Inc. 4-Port HUB
Bus 001 Device 005: ID 1a40:0101 Terminus Technology Inc. 4-Port HUB
Bus 001 Device 006: ID 07d1:3303 D-Link System DWA-131 802.11n Wireless N Nano Adapter(rev.A1) [Realtek RTL8192SU]
Bus 001 Device 007: ID 046d:c52e Logitech, Inc.</pre>

<del>果然是双核动力的hub，诚不我欺也，价格才29￥，比啥双核android手机给力多了。这个是真双核，非胶水双核。借用张淘宝图片，良心之作啊，强烈推荐。有了她，阿拉的派稳定多了</del>：

为了测试稳定性，连着两天没关机。今天给源源玩，打开显示器后玩了会，砰的一声，啥东西爆炸了，源吓坏了，我过去看，原来是这个HUB带的电源适配器烧掉了。屋里一股怪味。还没完，后来我又找了个电源适配器，沉甸甸的，质量应该不错。接上去又是一股怪味，一闻HUB本体烧了！悲剧啊，打开盒子看了下电路板，果然双核中有一核给浆爆了。检查了下，原来我找的电源适配器是9V输出的。看了下电路板，你妹，连个保险元件都没有，板子薄的和纸一样。看来在天朝捡便宜比中奖还难啊！（好消息是Pi本体在这次浩劫中经受住了考验。）

[<img class="alignnone size-full wp-image-1333" title="T1w2TiXf4kXXXR..k__110703.jpg_310x310" src="http://fkpwolf.net/WordPress/wp-content/uploads/2012/10/T1w2TiXf4kXXXR..k__110703.jpg_310x310.jpg" alt="" width="217" height="217" />][3]

## Post Installation:

1）配置无线网卡。raspbmc用的是NetworkManager的模式，配置方法见[这里][4]。xbian则是直接[修改][5]/etc/networks/interface。两者配置方法差别很大，不能强制套用。

2）设置终端字体大小。现在一般屏幕都很大，文本模式的话字体就显得太小了。改字体参考[这个][6]。

3）XMBC适用的遥控器。本来准备弄个红外（IR Remote）的，搜了下论坛，原来大多数电视机本身带有一个功能HDMI-CEC，就是用电视机本身的遥控器来控制多个HDMI输出设备，Cool！怪不得我的LG蓝光可以唤醒LG电视机（LG的CEC支持叫Simplink，这个每个电视厂商叫法都不同。）。<del>不过LG的CEC好像<a href="http://xbian.org/forum/viewtopic.php?f=35&t=1518">有点问题</a></del>。昨天晚上实验了下Raspbmc（因为这个是网络安装，所以感觉会有最新的更新），惊喜的发现这个在我家的LG上直接可以使用CEC，使用电视机遥控器可以直接控制XBMC，太cool了！原来HDMI还有这个功能。

在XBMC里面有个debug配置项，可以设置截图的目录。你可以设到NAS的SAMBA目录。然后在无线键盘上按PrtSc，你到NAS的目录上一看，截图已经有了。Cool！把玩一番，**不得感叹**XMBC太强了。功能齐全：密密麻麻的一堆配置项，而且UI看上去很专业，这个在开源界是很少见。其实国内一大堆做这种“高清硬盘播放器”，taobao上一搜一大堆，300￥就够了，基于android，功能N多。<del>为什么没有谁做个基于XMBC的呢</del>（XMBC 12.0已经可以安装在android上了）？可能技术人员对android移植更熟悉。

再次吐槽：为什么BeagleBone价格是RPi的两倍，可玩性为毛差多了呢？（当然，板子的做工BB还是好很多）除了BB要求高上手难，HDMI是关键！然后你看人家raspberrypi.org网站做的多仔细，还有论坛，相比较BB就一个Google Group，太寒碜了。所以你看到现在到处都是RPi的讨论&#8230;

4）显示中文。XMBC本身是支持中文的，在“System->Appearance->Skin->Fonts”中选择Arial based，然后再i18n里面设置为China即可。这样设置后，不仅界面全部变成中文，而且UPNP里面的目录名称也可以显示中文文件名的电影。字幕显示中文也没有问题。

5）使用UART接口。我用的是taobao上常见的PL2303 USB/UART模块，大概20￥。  
[<img class="size-thumbnail wp-image-1353 alignleft" title="T2f250XfhXXXXXXXXX_!!740676578" src="http://fkpwolf.net/WordPress/wp-content/uploads/2012/10/T2f250XfhXXXXXXXXX_740676578-150x150.jpg" alt="" width="150" height="150" />][7]

但是不推荐，因为这个不支持windows 8系统，[说是EOL][8]云云，还好提供OS X的驱动。新买的话我觉得不如买FTDI 232芯片的。没办法，换到苹果下，接上线&#8230;.. 折腾了半天都没有成功，后来[发现][9]线接反了：**要RPi的RX接pl2303的TX，RPi的TX接PL2302的RX**。VCC和GND都没有必要接，因为RPi已经由micro USB口供电了。然后用CoolTermMac很容易就连上去了，或者使用命令行“screen /dev/tty.usbserial 115200”，或者“minicom -D /dev/tty.usbserial -b 115200”。minicom可以“Ctrl+A Z X”退出，screen好像<del>只有强制退出的方式</del>(一样的：[CTRL-A]+K)。如果设置好后还是连不上，试试重启RPi，或者重新连接USB/UART模块。感觉TTY好像有会话的意思：前面一个没有正常退出，后面一个是连不上的。

&nbsp;

6）外接键盘。我试过罗技的MK240键鼠套装，2.4G无线，兼容性很好，也不需要配对，缺点就是USB的无线适配器个头大了点。后来入了个罗技的K700，优联技术，带小的触摸板，是Google TV的失败产物，taobao才70￥，很超值。不过配对时好时坏，奇怪的很。解决办法在[这里][10]，要重新load下kernel module。这个键盘有很多快捷键，可能对TV的操作模式做了优化。还不知道在linux如何mapping。如果能用起来的话还是很不错的。

7）字体。中文字体要安装package *ttf-wqy-zenhei*。默认的freetype渲染字体看上去很单薄，本来想打上ubuntu的patch，不过版本没对上，于是按照[这里][11]弄了个infinality的patch，在ARM下可以直接编译通过。效果感觉还行，就是窗口系统还是有点慢，体验比较差。

 [1]: http://forum.xbmc.org/showthread.php?tid=133722
 [2]: http://www.linux-magazine.com/w3/issue/03/EmbeddedGraphics.pdf
 [3]: http://fkpwolf.net/WordPress/wp-content/uploads/2012/10/T1w2TiXf4kXXXR..k__110703.jpg_310x310.jpg
 [4]: http://forum.stmlabs.com/showthread.php?tid=1620&pid=29372#pid29372
 [5]: http://xbian.org/forum/viewtopic.php?f=7&t=29
 [6]: http://ncloy.co.uk/blog/?p=356x
 [7]: http://fkpwolf.net/WordPress/wp-content/uploads/2012/10/T2f250XfhXXXXXXXXX_740676578.jpeg
 [8]: http://prolificusa.com/pl-2303hx-drivers/
 [9]: http://www.simonpainter.com/2012/08/simple-serial-hookup/
 [10]: http://www.raspberrypi.org/phpBB3/viewtopic.php?f=28&t=18675
 [11]: http://www.zfanw.com/blog/ubuntu-font-render-with-infinality.html