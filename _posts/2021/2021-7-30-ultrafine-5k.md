---
layout: post
title: "驱动 Ultrafine 5K 显示器"
date: 2021-7-30
categories:
  - DIY
typora-root-url: ../../../blog
---
### 各系统支持情况
Windows HiDPI 支持算是最好的：
- 可以支持更多 scale 倍数比如 250%
- 不同屏幕可以用不同 DPI
- 窗口拖动的时候可以跨屏显示，Mac 会截掉一半

对于 27 寸 5K 显示器，Windows 下面 250% 的放大比例最为完美。Gnome 下面 5K 200% 放大比在 4k 下表现好很多，4k 下 200% 放大会让窗口组件过大而显得不够精细， 而 [Linux Fractional Scaling](https://wiki.archlinux.org/title/HiDPI#Fractional_scaling) 还不成熟。

### 主板
- [MEG Z590I UNIFY](https://www.msi.com/Motherboard/MEG-Z590I-UNIFY) 有两个 DP IN 接口，只支持 11 代CPU，不支持 MacOS。但是两个 DP IN 算是雷电主板里面最高规格了，[BIOS](https://www.chiphell.com/portal.php?mod=view&aid=25543&page=4) 里面也有雷电相关的详细配置。[MEG Z590 ACE](https://www.msi.com/Motherboard/MEG-Z590-ACE)也有两个 DP IN。MSI MEG Z690 ACE 雷电也是一样配置。
- [ProArt B550-Creator](https://www.asus.com/Motherboards-Components/Motherboards/ProArt/ProArt-B550-CREATOR/), one DP-IN，不知道是否支持5K。 [BIOS](https://dlcdnets.asus.com/pub/ASUS/misc/Manual/PRIME_TUF_GAMING_B550_Series_BIOS_EM_WEB_EN.pdf)里面雷电选项还挺丰富。"PCIe 3.0 x4 will be switched from Thunderbolt™ 4 to PCIEX16_3 if the slot is populated; by then Thunderbolt™ 4 will have no output" Z490也是这样，因为没有足够的 PCI 通道数。这种情况下我猜显示通道还是可以的，因为显示和PCIe是分开的。
- [B550 VISION D-P](https://www.gigabyte.com/Motherboard/B550-VISION-D-P-rev-10#kf) all has one DP-IN. 2k. But not mATX board. [Manual](https://download.gigabyte.com/FileList/Manual/mb_manual_b550-vision-d-p_1001_e.pdf) doesn't show thunderbolt menu in detail - same as B550M AORUS. tonymacx86 [details guide](https://www.tonymacx86.com/threads/gigabyte-b550-vision-d-thunderbolt-3-amd-ryzen-7-3700x-amd-rx-5600-xt.304553/) on this board.
- [ProArt Z690-CREATOR WIFI](https://www.asus.com/Motherboards-Components/Motherboards/ProArt/ProArt-Z690-CREATOR-WIFI/techspec/)，两个 DP IN，PCIe 3.0 x4 和雷电并没有共享带宽，这个是比Z590强的地方。
- [ASRock X570 Phantom Gaming-ITX/TB3](https://www.gigabyte.com/Motherboard/X299X-DESIGNARE-10G-rev-10#kf) amazon.cn 1700¥. BIOS [Manual](https://download.asrock.com/Manual/X570%20Phantom%20Gaming-ITXTB3.pdf) has no thunderbolt info. 只有一个DP IN，似乎只能支持最高4K显示。
- [ASRock X570 Creator](http://www.asrock.com/mb/AMD/X570%20Creator/index.cn.asp#Overview)，这个板子神奇的地方在于后部面板有一个DP IN，这个和一般主板一样，但是板载上面有另外一个DP IN（有的显卡内置一个mini DP），华擎果真出妖板！网上有不少用这个板子接 Ultrafine 5k运行黑苹果的，比如[这里](https://forum.amd-osx.com/index.php?threads/asrock-x570-creator-ryzen-9-3900x-rx6900xt-lg-ultrafine-5k-2x-thunderbolt-3-opencore-0-7-1-working.1984/)和[这里](https://www.reddit.com/r/hackintosh/comments/djgulk/asrock_x570_creator_ryzen_3400g_ati_radeon_rx_570/)。
- [PRIME X299-DELUXE II](https://www.asus.com/us/motherboards-components/motherboards/all-series/prime-x299-deluxe-ii/), 技嘉 [X299X AORUS Designare](https://www.gigabyte.com/Motherboard/X299X-DESIGNARE-10G-rev-10#kf) 都有两个 DP IN，两个雷电口。就是板子太贵，平台有点老，CPU 似乎还可以，7940x/7960x/7980xe 价格不贵。
- Z490主板里面支持 2 DP IN 的只有华硕 [PROART Z490-CREATOR 10G](https://www.asus.com/Motherboards-Components/Motherboards/ProArt/ProArt-Z490-CREATOR-10G/)，而且 [BIOS](https://dlcdnets.asus.com/pub/ASUS/mb/13MANUAL/PRIME_PRO_PROART_TUF_GAMING_Intel_400_Series_BIOS_EM_WEB_EN.pdf) 里面的功能也很全。[完整](https://www.olarila.com/topic/13305-olarila-hackbeast-proart-z490-creator-10g-with-thunderbolt-full-dsdt-patches-clover-or-opencore-guide-and-discussion/)的OpenCore文件，看来已经被研究的很透彻了。可惜规格这么高的z490大板只有少的可怜的 PCI 通道 & CPU 太老，真实使用起来和我的 B460m 差不多吧。10代Intel CPU 中 10850k 比较有性价比，10核心20线程，2400¥。
- 超微[X11SRA](https://www.supermicro.com/en/products/motherboard/X11SRA)有雷电 header，但是 BIOS 不支持，这么多PCI，真是可惜了。[这里说](https://www.chiphell.com/forum.php?mod=viewthread&tid=2153615&extra=page%3D1&mobile=1)可以在没有 header 的超微 X11DPH-T 上面实验成功，奇怪了，多半是默认开启，但是 pre-boot 肯定是没有了。
- [Pro WS W480-ACE](https://www.asus.com/Motherboards-Components/Motherboards/Workstation/Pro-WS-W480-ACE/)，两 DP IN，支持 CPU 有 10 代，11 代和 Xeon W，不过 PCI 通道数似乎没啥亮点，有点尴尬。

### 雷电扩展卡
虽然御三家都有雷电控制卡，做的比较好的是[GC-TITAN-RIDGE v2](https://www.gigabyte.com/Motherboard/GC-TITAN-RIDGE-rev-20#kf)和[GC-TITAN-RIDGE](https://www.gigabyte.com/Motherboard/GC-TITAN-RIDGE-rev-10#kf)。居然连 AMD B550 也支持，[B550M AORUS PRO](https://www.gigabyte.com/Motherboard/B550M-AORUS-PRO-rev-10/sp#sp) 感觉这个还可以，DP 接口支持 5K，如果配个 APU 就可以省下显卡钱了。
https://www.chiphell.com/thread-1955259-1-1.html TITAN RIDGE 不需要雷电header就可以，有时候需要短接。
Z490支持DP IN的，也就是支持5k输出的是[Z490 VISION D](https://www.gigabyte.com/Motherboard/Z490-VISION-D-rev-1x#kf)。
https://cn.msi.com/Motherboard/Z490M-S1/Specification 这个主板规格比较完美，不过是微星的，技嘉的扩展卡不知道是否兼容。或者[MAG B460M MORTAR](https://www.msi.com/Motherboard/MAG-B460M-MORTAR) 迫击炮感觉也行，有雷电口5pin JTBT1，这个OC支持的更多 https://heipg.cn/tutorial/b460m-install-big-sur.html。好玩是的从[规格书](https://download.msi.com/archive/mnu_exe/mb/E7C82v1.1.pdf)上看是微星这个板子有两个thunderbolt接口:
![](/images/2021/thunderbolt-port.png)
GC-TITAN-RIDGE-20的[规格书](https://download.gigabyte.com/FileList/Manual/mb_manual_GC-TITAN-RIDGE-20_1001.pdf)也有这两个接口。Z490/B560M AORUS PRO AX技嘉的板子[也有](https://download.gigabyte.com/FileList/Manual/mb_manual_z490-aorus-pro-ax_1001_e.pdf)两个雷电header，B550M AORUS PRO则只有一个。问题是技嘉的扩展卡和主板都没有详细列出header的定义，所以不确定微星的主板是否和技嘉的扩展卡兼容。这个 RTD3 好像是“[现代待机](https://zhuanlan.zhihu.com/p/114448236)”（[Modern Standby](https://graniteriverlabs.com.tw/2021/04/07/grl-ap-tbt-fv-modern-standby/)）的东西。

技嘉Titan ridge v1.0使用：固件要升级，否则无法视频输出。主板没有thunderbolt header，1//3要短接，USB 接口要连接，否则难以激活。

确实，我修改NUC 8 BIOS后为“无安全性(SL0)”，但是我也没法在技嘉主板BIOS里面修改。<https://github.com/ameyrupji/thunderbolt-macpro-5-1/blob/master/GC-TitanRidge.md> 这里有刷机的步骤。后来发现这个技嘉板子里面可以修改，修改后进入thunderbolt controller center也能看到security level改变了，我改为none。但是启动后发现雷电下面usb无法链接，重启也不行，只有热拔插雷电线可以，只能改回默认的 user auth。似乎这个如果bios里面修改了值，bios启动时会修改雷电扩展卡配置，但是不知道是因为和主板不兼容，还是我没有连接header，导致启动后OS虽然能看到修改后的安全级别，但是功能出现问题。这个是我的猜测。

### B550M AORUS 
BIOS 没有pre-boot acl支持怎么办？可以试试去掉mortar的雷电header改为短接，如果pre-boot还是可以工作，那说明这个功能完全是由bios控制。
用AMIBCP看了b550m vision d板子的Bios，选项也很少。不是说技嘉的雷电支持是最好的么？
这个板子问题也挺多：
1. 关机变成重启，我再BIOS里面关掉了ethernet weak on lan，可以关机，但是在windows日志里面还是有意外关机的错误
2. 换回 Titan Ridge v1.0 后在 user auth 安全模式下似乎稳定了些。
3. 关掉windows的快速启动，在user auth模式下似乎很稳定了。no auth还是老问题。
4. B550M AORUS AX 没有 THB 插座， 使用"测试架PCB夹子治具夹具探针"接上thb 5pin口，问题依然，和短接没区别，只是可以正常关机了。看来要pre-boot 键盘支持的话必须bios支持
5. user auth下，发现关机后无法连接，后来还是必须关掉windows的快速启动
6. Linux 下suspend会导致机器风扇狂转，无法通过关机键关机，只能关掉电源按钮。试过不同Linux版本同样的问题。Windows下可以休眠。感觉还是和雷电卡有关系，将来升级BIOS可能会修复这个问题 (确实，会好点，现在是suspend变成poweroff)。
7. 今天发现 F4 版本 BIOS 已经出现 pre-boot 菜单，太棒了！但是设置后并不起效，甚至连 no security 也不能生效（Thunderbolt控制中心还是显示用户身份认证），更难以忍受的是更新后BIOS，开机自检到显示BIOS画面中间要等半天。只能换回F2版本。
8. 闲鱼买了个B550m Aorus Pro-P，带 THB 接口。接上去后和上面 Aorus AX 总体表现一致。开机会快点。刷最新 BIOS 后，贝尔金雷电扩展坞总可以正常工作，但是 Ultrafine 显示器自带的 USB 设备总是不没法正常工作，而这个显示器的声音设备对于我来说是必须的。而且 Windows 下面还会有 Cache Hierarchy Error，会随机重启，这个很恐怖，网上说这个大概和 CPU 电压配置有关。换成低版本的 BIOS 后表现和 AX 一致。看来这个设备 BIOS 对于不同雷电设备还是有兼容性问题。
9. B550m Aorus Pro-P最新的F15a版本离上个版本只有一个月，像紧急修复。首先稳定提高，不会出现Windows随机启动的问题。然后bios设置雷电no security也能在windows中生效，所以上面两个问题得到修复。

### MAG B460M MORTAR
v11 BIOS菜单里面有雷电，虽然问题多多（雷电设备能看到，但是USB和PCI网卡都看不到）。但是 v14 居然去掉了。v13 changelog 我看还有改进 Thunderbolt functional patch. 换成 v13，问题解决。

Security Level我如果改成none，和上面技嘉B550m一样的问题：usb 无法连接。有时候第一次可以，但是重启后就不行了。改成user auth好很多，但这样和上面其实效果一样。所以关键还是在这个雷电扩展卡？这个板子bios里面雷电选项很多，比如从雷电启动。
![](/images/2021/MSI_board_thunderbolt.png)
这里设置 pre-boot ACL 就可以用接在雷电上面的鼠标键盘进入 BIOS 了，不管这个键盘是接在一级还是二级雷电设备下。微星的这个 BIOS 太强了！[这里](https://www.chiphell.com/portal.php?mod=view&aid=25543&page=4)看MSI MEG Z590I Unify也是支持pre-boot ACL的了。
为什么冥王峡谷反而没有？[这里说](https://drivers.softpedia.com/get/MOTHERBOARD/Intel/Intel-NUC8i7HVK-NUC-Kit-Thunderbolt-Driver-17-3-72-250-for-Windows-10-64-bit.shtml)这个版本的驱动是支持的啊：
```
Added support for PRE-BOOT ACL feature (deprecating previous implementation from 17.1.64.250): Devices that can be used for boot or during pre-boot need to be pre-approved. Pre-Boot ACL solution enables device pre boot approval by maintaining an access control list in the FW as well
```
但是 NUC BIOS 里面没有看到对应的配置。看看其thunderbolt nvm version is just 33.0，但是 NUC 官网也没有新的版本。我猜测进入windows系统后动态的启用和批准设备的pre boot，这样重启后启动就能在启动阶段使用键盘。后来测试这个NUC对pre-boot支持的已经挺好了，虽然bios里面没有显示的支持，默认似乎是已经开启了。

### ProArt Z690-Creator
挺完美的主板，从[这里](https://www.computerdiy.com.tw/asus-proart-z690-creator-wifi_unbox/)的Block diagram看，其雷电口是可以输出集显DP显示的，如果同时接上DP IN，如何切换显示源呢？还是默认接上外接显卡集显就被屏蔽了？
![](/images/2021/paroart-z690.png)
[这里开箱](http://enterbox.tw/8882)显示主控为 JHL8540 ThunderBolt4 控制器。tonymacx86 已经有 macOS 安装[方法](https://www.tonymacx86.com/threads/asus-z690-proart-creator-wifi-thunderbolt-4-i7-12700k-amd-rx-6800-xt.318311/)，虽然我不是很想试。

### 雷电 4
https://www.gigabyte.com/Motherboard/GC-MAPLE-RIDGE-rev-10#kf 这个表明支持5k，我看也是Display Port 1.4。[这个](https://www.asrock.com/MB/Intel/Z590%20Phantom%20Gaming-ITXTB4/index.asp#Specification)也可以。https://www.msi.com/Motherboard/MAG-B560M-MORTAR/Specification 微星这个性价比的选择1x TBT connector (Supports RTD3) 。这个B560M AORUS PRO AX[白雕](https://www.gigabyte.com/Motherboard/B560M-AORUS-PRO-AX-rev-10)也挺好看。
[Asus Thunderbolt EX3-TR](https://www.asus.com.cn/support/FAQ/1043485/)，新版本，居然不支持mATX的板子。14-1 pin Thunderbolt，这个只有asus板子才有这个规格吧。

Kensington Thunderbolt 4 有 4 个雷电口，看上去不错，有点像交换机了。

### 连接显卡
<https://superuser.com/questions/845302/what-specs-must-a-graphics-card-have-to-power-a-5k-monitor>
<https://images.nvidia.com/aem-dam/en-zz/Solutions/design-visualization/quadro-product-literature/NV-DS-NVS-310-May12-US-NV.pdf>
<https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/quadro-product-literature/NVS_510_DS_Oct12_NVIDIA_LR.pdf>
都支持MST，但是记得安装最新驱动。安装后直接显示一个5k显示器，否则会显示两个屏幕。

以前是ultrafine的声音设备只有先进入windows，然后重启进入macOS后，macos可以认识声音设备，但是刷bios后这个问题消失了。
似乎是[pre-boot ACL](https://christian.kellner.me/2019/02/11/thunderbolt-preboot-access-control-list-support-in-bolt/)问题，但是我这个应该要linux和macos自身的问题，也就是关机时设置雷电外设。
https://fedoramagazine.org/thunderbolt-how-to-use-keyboard-during-boot-time/

如果type-c或者雷电线支持视频，USB和声音传输，这就有点像KVM功能了。

“对于5k，此显示器同时支持MST和SST。用雷电卡，将双路DP1.2合并到USB-C形状的雷电，直接MST方式驱动即可；另外新款N卡，支持DP1.4输出即采用SST方式驱动，因WIN10同时支持MST和SST，所以两种方式很容易驱动。但是HACKINTOSH驱动5k只能采取MST方式，系统原生不支持SST驱动5k，所以只能采取win10的第一种方式，即利用雷电卡将两路DP1.2合并到USB-C形状的雷电，驱动显示器。” Come from [here](https://www.chiphell.com/forum.php?mod=redirect&goto=findpost&ptid=2327490&pid=47599622). 从[这里](https://www.futureplus.com/displayport-multi-steam-versus-single-stream-what-is-the-difference/)解释来看，SST 是最简单的形式，MST 一般用在DP菊链中。

贝尔金CV10 线(DP转 TYPEC) 据说可以直接搞定，不过不是雷电方案，要拖几根usb线，感觉违背了雷电初衷。Moshi USB C to DisplayPort Cable 这跟线也支持 5K，但是就没有USB通道了。

[Type-C 扩展器 方案参考（Thunderbolt Alternative Mode 篇）](https://zhuanlan.zhihu.com/p/390425571) 专业的雷电分析，作者雷电系列和拆解也不错。

### 雷电扩展坞
* OWC Thunderbolt 3 Pro 配有 10 Gb 以太网连接
* startech.com thunderbolt 3 DOCK 感觉就这个便宜点
* HP雷电扩展坞 [HP Elite 65W Thunderbolt 3 Dock](https://support.hp.com/us-en/drivers/selfservice/hp-elite-thunderbolt-3-65w-dock/9822172)，如果接usb键盘鼠标，冷启动OpenCore中无法操作键盘鼠标，重启可以操作。这个扩展坞在 x86 Mac下面无法免驱，但是在 m1 Mac mini下面是免驱的。
为什么 ultrafine 的usb键盘可以操作呢？usb 2.0 hub？ultrafine也不是所有情况都可以：只有windows关机和重启后才可以。如果是linux关机后开机，一样问题。
上面的ASM1042A要到HP官网上才能下载驱动，Windows 默认没有带驱动，真是辣鸡啊，VGA 口也很唐突，后来出了。
* Belkin Thunderbolt 3 Express Dock HD, F4U095，<https://www.belkin.com/th/support-article?articleNum=216428> 没有windows驱动和固件更新下载，据说Windows下面没法PD输出电流，买的有点唐突。

### 总结
总的看来，[雷电](https://zh.wikipedia.org/wiki/Thunderbolt)的理念确实不错：一根线能传输电和数字信号，这对桌面的整洁至关重要，特别适合某些场景：

1. 笔记本接显示器工作模式，可以说雷电的最早就是为 MacBook 设计的。
2. 多个台式机共享显示器。一般用 KVM 切换来解决这个问题，但是如果接高分辨率显示器，KVM 价格就很贵了。

可惜的是，因为是 Intel 和 Apple 合作的方案，其他系统和平台存在不少兼容性问题。
