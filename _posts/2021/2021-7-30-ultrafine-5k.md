---
layout: post
title: "驱动 Ultrafine 5K 显示器"
date: 2021-7-30
categories:
  - DIY
typora-root-url: ../../../blog
---
### 操作系统支持情况
Windows HiDPI 支持是最好的：
- 可以支持更多 scale 倍数，比如 250%
- 不同屏幕可以用不同 DPI
- 窗口拖动的时候可以跨屏显示，Mac 会截掉一半

对于 27 寸 5K 显示器：
* Windows 下面 250% 的放大比例最为完美，此时 web page 显示 width 为 2048px。
* Gnome 下面 5K 200% 放大比在 4k 下表现好很多，虽然整体有些偏小，而 300% 放大又明显过大。4k 下 200% 放大会让窗口组件过大而显得不够精细， 而 [Linux Fractional Scaling](https://wiki.archlinux.org/title/HiDPI#Fractional_scaling) 还不成熟（现在已经支持 250% 放大比例）。作为折中办法，我把浏览器统一放大到125%。Gnome 在 200% 放大比例下，最好显示效果是32寸4k。
* MacOS 下面 200% 放大字体偏小，没有 4k 显示效果好。系统没有其他放大比例。

### 主板
- [MEG Z590I UNIFY](https://www.msi.com/Motherboard/MEG-Z590I-UNIFY) 有两个 DP IN 接口，只支持 11 代CPU，不支持 MacOS。但是两个 DP IN 算是雷电主板里面最高规格了，[BIOS](https://www.chiphell.com/portal.php?mod=view&aid=25543&page=4) 里面也有雷电相关的详细配置。[MEG Z590 ACE](https://www.msi.com/Motherboard/MEG-Z590-ACE)也有两个 DP IN。MSI MEG Z690 ACE 雷电也是一样配置。这些主板都是通过PCIe子卡插到主板上，所以可能无法连通集显，也就是说雷电只能通过DP-IN来输出显示信号，效果和技嘉雷电卡一样。
- [ProArt B550-Creator](https://www.asus.com/Motherboards-Components/Motherboards/ProArt/ProArt-B550-CREATOR/), one DP-IN，不知道是否支持5K。 [BIOS](https://dlcdnets.asus.com/pub/ASUS/misc/Manual/PRIME_TUF_GAMING_B550_Series_BIOS_EM_WEB_EN.pdf)里面雷电选项还挺丰富。"PCIe 3.0 x4 will be switched from Thunderbolt™ 4 to PCIEX16_3 if the slot is populated; by then Thunderbolt™ 4 will have no output" Z490也是这样，因为没有足够的 PCI 通道数。这种情况下我猜显示通道还是可以的，因为显示和PCIe是分开的。
- [B550 VISION D-P](https://www.gigabyte.com/Motherboard/B550-VISION-D-P-rev-10#kf) all has one DP-IN. 2k. But not mATX board. [Manual](https://download.gigabyte.com/FileList/Manual/mb_manual_b550-vision-d-p_1001_e.pdf) doesn't show thunderbolt menu in detail - same as B550M AORUS. tonymacx86 [details guide](https://www.tonymacx86.com/threads/gigabyte-b550-vision-d-thunderbolt-3-amd-ryzen-7-3700x-amd-rx-5600-xt.304553/) on this board.
- [ProArt Z690-CREATOR WIFI](https://www.asus.com/Motherboards-Components/Motherboards/ProArt/ProArt-Z690-CREATOR-WIFI/techspec/)，两个 DP IN，PCIe 3.0 x4 和雷电并没有共享带宽，这个是比Z590强的地方。
- [ASRock X570 Phantom Gaming-ITX/TB3](https://www.gigabyte.com/Motherboard/X299X-DESIGNARE-10G-rev-10#kf) amazon.cn 1700¥. BIOS [Manual](https://download.asrock.com/Manual/X570%20Phantom%20Gaming-ITXTB3.pdf) has no thunderbolt info. 只有一个DP IN，似乎只能支持最高4K显示。
- [ASRock X570 Creator](http://www.asrock.com/mb/AMD/X570%20Creator/index.cn.asp#Overview)，这个板子妖的地方在于后部面板有一个DP IN，板载另外一个DP IN（有的显卡内置一个mini DP），华擎果真出妖板！网上有不少用这个板子接 Ultrafine 5k运行黑苹果的，比如[这里](https://forum.amd-osx.com/index.php?threads/asrock-x570-creator-ryzen-9-3900x-rx6900xt-lg-ultrafine-5k-2x-thunderbolt-3-opencore-0-7-1-working.1984/)和[这里](https://www.reddit.com/r/hackintosh/comments/djgulk/asrock_x570_creator_ryzen_3400g_ati_radeon_rx_570/)。
- [PRIME X299-DELUXE II](https://www.asus.com/us/motherboards-components/motherboards/all-series/prime-x299-deluxe-ii/), 技嘉 [X299X AORUS Designare](https://www.gigabyte.com/Motherboard/X299X-DESIGNARE-10G-rev-10#kf) 都有两个 DP IN，两个雷电口。就是板子太贵，平台有点老，CPU 似乎还可以，7940x/7960x/7980xe 价格不贵。
- Z490主板里面支持 2 DP IN 的只有华硕 [PROART Z490-CREATOR 10G](https://www.asus.com/Motherboards-Components/Motherboards/ProArt/ProArt-Z490-CREATOR-10G/)，而且 [BIOS](https://dlcdnets.asus.com/pub/ASUS/mb/13MANUAL/PRIME_PRO_PROART_TUF_GAMING_Intel_400_Series_BIOS_EM_WEB_EN.pdf) 里面的功能也很全。[完整](https://www.olarila.com/topic/13305-olarila-hackbeast-proart-z490-creator-10g-with-thunderbolt-full-dsdt-patches-clover-or-opencore-guide-and-discussion/)的OpenCore文件，看来已经被研究的很透彻了。可惜规格这么高的z490大板只有少的可怜的 PCI 通道 & CPU 太老，真实使用起来和我的 B460m 差不多吧。10代Intel CPU 中 10850k 比较有性价比，10核心20线程，2400¥。
- 超微[X11SRA](https://www.supermicro.com/en/products/motherboard/X11SRA)有雷电 header，但是 BIOS 不支持，这么多PCI，真是可惜了。[这里说](https://www.chiphell.com/forum.php?mod=viewthread&tid=2153615&extra=page%3D1&mobile=1)可以在没有 header 的超微 X11DPH-T 上面实验成功，奇怪了，多半是默认开启，但是 pre-boot 肯定是没有了。
- [Pro WS W480-ACE](https://www.asus.com/Motherboards-Components/Motherboards/Workstation/Pro-WS-W480-ACE/)，两 DP IN，支持 CPU 有 10 代，11 代和 Xeon W，不过 PCI 通道数似乎没啥亮点，有点尴尬。
- AM5 主板[支持情况](https://unikoshardware.com/2022/08/amd-am5-usb4-ic-shortage.html)，需要一个USB4芯片。[技嘉 X670E Aero D](https://www.techpowerup.com/295196/hands-on-with-the-new-gigabyte-x670-motherboards-at-computex-2022)用的是 ASMedia ASM4242，没有看到DP IN的选项，用的都是 AM5 CPU 新内建的核显。不过这块主板看来是难产了。[MSI USB4 Expansion Card MS-4489](https://www.techpowerup.com/309532/msi-first-motherboard-maker-to-offer-usb4-add-in-card-with-100-w-usb-pd)用的也是ASM4242，这卡现在叫[USB4 PD100W EXPANSION CARD](https://www.msi.com/PC-Component/USB4-PD100W-EXPANSION-CARD/Overview)。华硕也出了款[USB4 PCIE GEN4 CARD](https://www.asus.com/motherboards-components/motherboards/accessories/usb4-pcie-gen4-card/techspec/)。
- Type-C 显示输出主板，比如[B650I AORUS ULTRA](https://www.gigabyte.com/Motherboard/B650I-AORUS-ULTRA-rev-10#kf)，这个主板也支持3个M.2。这种没有DP IN的用来配合APU很合适，但是可能主板价格比CPU贵。不确定是否能驱动这款显示器。X670E-I双雷电，没有DP IN，[这里](https://www.reddit.com/r/ASUS/comments/123mgjx/asus_x670ei_supports_lg_ultrafine_5k/)显示这种集成显卡的雷电输出方式都包含了两条DisplayPort信号，而且集成显卡的输出可以用独立显卡来加速，和笔记本非直连功能一样。
- ASRock [Z790 PG-ITX/TB4](https://pg.asrock.com/mb/Intel/Z790%20PG-ITXTB4/index.asp)，没有 DP IN 接口。
- [B650E AORUS PRO X USB4](https://www.gigabyte.com/Motherboard/B650E-AORUS-PRO-X-USB4-rev-10) 使用[ASM4242](https://unikoshardware.com/2024/06/gigabyte-b650e-aorus-and-x870e-leak.html)连接CPU的PCIe通道。其实就是超级雕的一个cpu m.2口接了ASM4242，但是没有DP IN。这个主板也带 thb header，所以可以组成 4 雷电接口。

### 雷电扩展卡
虽然御三家都有雷电控制卡，做的比较好的是[GC-TITAN-RIDGE v2](https://www.gigabyte.com/Motherboard/GC-TITAN-RIDGE-rev-20#kf)和[GC-TITAN-RIDGE](https://www.gigabyte.com/Motherboard/GC-TITAN-RIDGE-rev-10#kf)。居然连 AMD B550 也支持，[B550M AORUS PRO](https://www.gigabyte.com/Motherboard/B550M-AORUS-PRO-rev-10/sp#sp) 感觉这个还可以，DP 接口支持 5K，如果配个 APU 就可以省下显卡钱了。
TITAN RIDGE 不需要雷电header就可以，有时候需要[短接](https://www.chiphell.com/thread-1955259-1-1.html)。
Z490支持DP IN的，也就是支持5k输出的是[Z490 VISION D](https://www.gigabyte.com/Motherboard/Z490-VISION-D-rev-1x#kf)。
[Z490M-S1](https://cn.msi.com/Motherboard/Z490M-S1/Specification)这个主板规格比较完美，不过是微星的，技嘉的扩展卡不知道是否兼容。或者[MAG B460M MORTAR](https://www.msi.com/Motherboard/MAG-B460M-MORTAR) 迫击炮感觉也行，有雷电口5pin JTBT1，这个OC支持的更多 https://heipg.cn/tutorial/b460m-install-big-sur.html。好玩是的从[规格书](https://download.msi.com/archive/mnu_exe/mb/E7C82v1.1.pdf)上看是微星这个板子有两个thunderbolt接口:
![](/images/2021/thunderbolt-port.png)
GC-TITAN-RIDGE-20的[规格书](https://download.gigabyte.com/FileList/Manual/mb_manual_GC-TITAN-RIDGE-20_1001.pdf)也有这两个接口。Z490/B560M AORUS PRO AX技嘉的板子[也有](https://download.gigabyte.com/FileList/Manual/mb_manual_z490-aorus-pro-ax_1001_e.pdf)两个雷电header，B550M AORUS PRO则只有一个。问题是技嘉的扩展卡和主板都没有详细列出header的定义，所以不确定微星的主板是否和技嘉的扩展卡兼容。这个 RTD3 好像是“[现代待机](https://zhuanlan.zhihu.com/p/114448236)”（[Modern Standby](https://graniteriverlabs.com.tw/2021/04/07/grl-ap-tbt-fv-modern-standby/)）的东西。技嘉Titan ridge v1.0使用：固件要升级，否则无法视频输出。主板没有thunderbolt header，1//3要短接，USB 接口要连接，否则难以激活。确实，我修改NUC 8 BIOS后为“无安全性(SL0)”，但是我也没法在技嘉主板BIOS里面修改。<https://github.com/ameyrupji/thunderbolt-macpro-5-1/blob/master/GC-TitanRidge.md> 这里有刷机的步骤。后来发现这个技嘉板子里面可以修改，修改后进入thunderbolt controller center也能看到security level改变了，我改为none。但是启动后发现雷电下面usb无法链接，重启也不行，只有热拔插雷电线可以，只能改回默认的 user auth。似乎这个如果bios里面修改了值，bios启动时会修改雷电扩展卡配置，但是不知道是因为和主板不兼容，还是我没有连接header，导致启动后OS虽然能看到修改后的安全级别，但是功能出现问题。这个是我的猜测。

USB4 扩展卡：
* [微星啟動！MSI USB4 PD100W PCI-E 擴充卡開拓 USB 全新領域！](https://unikoshardware.com/2023/11/msi-usb4-pd100w-expansion-card.html)，看上去不错，但是兼容主板很少。
* [ASUS USB4 PCIe Gen4 Card](https://www.asus.com/motherboards-components/motherboards/accessories/usb4-pcie-gen4-card/)兼容不少主板，文档一直在更新。
这些扩展卡都使用ASM4242，但是尴尬的是这代主板不少都内置了雷电功能，所以如果不想浪费，最好用在中端的AMD主板。Intel因为 800 系列 CPU 已经内置了雷电控制器，自己加太多余。

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
7. 今天发现 F4 版本 BIOS 已经出现 Pre-Boot ACL 菜单，太棒了！但是设置后并不起效，甚至连 no security 也不能生效（Thunderbolt控制中心还是显示用户身份认证），更难以忍受的是更新后BIOS，开机自检到显示BIOS画面中间要等半天。只能换回F2版本。
8. 闲鱼买了个B550m Aorus Pro-P，带 THB 接口。接上去后和上面 Aorus AX 总体表现一致。开机会快点。刷最新 BIOS 后，贝尔金雷电扩展坞总可以正常工作，但是 Ultrafine 显示器自带的 USB 设备总是不没法正常工作，而这个显示器的声音设备对于我来说是必须的。而且 Windows 下面还会有 Cache Hierarchy Error，会随机重启，这个很恐怖，网上说这个大概和 CPU 电压配置有关。换成低版本的 BIOS 后表现和 AX 一致。
9. B550m Aorus Pro-P最新的F15a版本离上个版本只有一个月，像紧急修复。首先稳定提高，不会出现Windows随机启动的问题。然后bios设置雷电no security也能在windows中生效，所以上面两个问题得到修复。但是 Ultrafine 5K还是有兼容性问题：必须 enable User security & disable pre-boot，否则机器重启后找不到USB设备。如果连在贝尔金雷电扩展坞上面则表现很稳定。

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

B660M AORUS PRO DDR4，这个板子上面表现也很稳定，没有雷电相关的BUG。看上去 intel 芯片的板子雷电兼容性会好很多，可能是intel firmware做得好。

### ProArt Z690-Creator
![](/images/2021/paroart-z690.png)
挺完美的主板，从[这里](https://www.computerdiy.com.tw/asus-proart-z690-creator-wifi_unbox/)的Block diagram看，其雷电口是可以输出集显一个DP 1.4 来显示的，中间通过一个Q-SW来切换。板子本身没有DP输出。如果同时接上显卡DP IN，如何切换显示源呢？还是默认接上外接显卡集显就被屏蔽了？[这里开箱](https://zhuanlan.zhihu.com/p/434899023)显示主控为 JHL8540 ThunderBolt4 控制器。tonymacx86 已经有 macOS 安装[方法](https://www.tonymacx86.com/threads/asus-z690-proart-creator-wifi-thunderbolt-4-i7-12700k-amd-rx-6800-xt.318311/)，虽然我不是很想试。ProArt Z790 也是同样的高规格，没有找到其Block diagram，但是应该和上面一样，毕竟CPU规格并没有变。

ProArt X670只有一个DP IN，感觉这个是无法驱动Ultrafine 5K 显示器的。
![](/images/2021/ASRock_X670E-Taichi-block-diagram.jpg)
X670E太极没有DP IN，但是把两个CPU集成的显卡DP信号输出到了雷电口，所以这个应该可以直接能点亮 Ultrafine 5K 显示器。缺点在于集成显卡有点弱，没法像外接显卡那样能够升级。

### NUC / 迷你小主机 / 笔记本
NUC 设备一般都配有双雷电口，但是基本上都是连接的集成显卡DP输出，即使像蝰蛇峡谷这种配备 A770M 独立显卡的也不例外。只有冥王峡谷雷电口是直连的Radeon显卡。
当然，即使非直连的输出也能使用独立显卡的资源，这和游戏笔记本一样。

AMD 7840HS 小主机最近很流行，比如零刻 GTR7 配备了两个USB4.0接口，实测可以驱动这块5K显示器。详细见[这里](../2024/2024-7-1-bee-link-GTR7.md)的评测。

最近发布的 [Intel Core Ultra](https://www.anandtech.com/show/21282/intel-core-ultra-7-115h-review-meteor-lake-makes-makes-fresh-start-to-mobile-cpus) 带有 4 个雷电4接口，很恐怖（两条PCIe X4的带宽给雷电输出？）。
不过一般笔记本不会全部配齐，我看现在只有 ThinkBook X 2024 配备了3个雷电口。如果这个CPU做成迷你小主机，扩展无敌了。

### 雷电 4
[Maple Ridge](https://www.gigabyte.com/Motherboard/GC-MAPLE-RIDGE-rev-10) 这个表明支持5k，我看也是Display Port 1.4。[这个](https://www.asrock.com/MB/Intel/Z590%20Phantom%20Gaming-ITXTB4/index.asp#Specification)也可以。https://www.msi.com/Motherboard/MAG-B560M-MORTAR/Specification 微星这个性价比的选择1x TBT connector (Supports RTD3) 。这个B560M AORUS PRO AX[白雕](https://www.gigabyte.com/Motherboard/B560M-AORUS-PRO-AX-rev-10)也挺好看。
[Asus Thunderbolt EX3-TR](https://www.asus.com.cn/support/FAQ/1043485/)，新版本，居然不支持mATX的板子。14-1 pin Thunderbolt，这个只有asus板子才有这个规格吧。

雷电4对比3虽然带宽一样，但是：
* 带宽动态分配。雷电3给5K图像传输预留了固定得18Gb，其他数据只有22Gb，这个划分是固定的。而雷电4/USB4采用了隧道的方式，包含USB 3.2 隧道，DisplayPort 1.4a 隧道和PCIe 隧道，因为是动态分配，当其他两个隧道关闭时PCIe 隧道最大可以达32Gb，而雷电3时这个速度最大只有16Gb。
* CPU集成雷电4控制器，这能降低通信延迟。但是上面ProArt Z690-Creator架构图里面好像还是在PCH里面？只有在笔记本里面才是这样？

### 连接显卡
<https://superuser.com/questions/845302/what-specs-must-a-graphics-card-have-to-power-a-5k-monitor>
<https://images.nvidia.com/aem-dam/en-zz/Solutions/design-visualization/quadro-product-literature/NV-DS-NVS-310-May12-US-NV.pdf>
<https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/quadro-product-literature/NVS_510_DS_Oct12_NVIDIA_LR.pdf>
都支持MST，但是记得安装最新驱动。安装后直接显示一个5k显示器，否则会显示两个屏幕。

以前是ultrafine的声音设备只有先进入windows，然后重启进入macOS后，macos可以认识声音设备，但是刷bios后这个问题消失了。
似乎是[pre-boot ACL](https://christian.kellner.me/2019/02/11/thunderbolt-preboot-access-control-list-support-in-bolt/)问题，但是我这个应该要linux和macos自身的问题，也就是关机时设置雷电外设。
https://fedoramagazine.org/thunderbolt-how-to-use-keyboard-during-boot-time/

如果type-c或者雷电线支持视频，USB和声音传输，这就有点像KVM功能了。

“对于5k，此显示器同时支持MST和SST。用雷电卡，将双路DP1.2合并到USB-C形状的雷电，直接MST方式驱动即可；另外新款N卡，支持DP1.4输出即采用SST方式驱动，因WIN10同时支持MST和SST，所以两种方式很容易驱动。但是HACKINTOSH驱动5k只能采取MST方式，系统原生不支持SST驱动5k，所以只能采取win10的第一种方式，即利用雷电卡将两路DP1.2合并到USB-C形状的雷电，驱动显示器。” Come from [here](https://www.chiphell.com/forum.php?mod=redirect&goto=findpost&ptid=2327490&pid=47599622). 从[这里](https://www.futureplus.com/displayport-multi-steam-versus-single-stream-what-is-the-difference/)解释来看，SST 是最简单的形式，MST 一般用在DP菊链中。但是在我的测试中，6600显卡，接单根DP，ultrafine 5k只能显示4k。这个显卡一个DP口应该能驱动5K分辨率。这说明ultrafine 5k并不支持5k的SST驱动，必须使用两路DP1.2聚合的形式。

贝尔金CV10 线(DP转 TYPEC) 据说可以直接搞定，不过不是雷电方案，要拖几根 USB 线，感觉违背了雷电初衷。我尝试结果是能驱动Ultrafine 5k，包括显示器的音箱和摄像头，但是分辨率最高只能4k。如果不直连显示器，通过雷电hub，无法输出显示信号。这个线应该能驱动5k的Studio Display。

Moshi USB C to DisplayPort Cable 这跟线也支持 5K，但是就没有USB通道了。

[Type-C 扩展器 方案参考（Thunderbolt Alternative Mode 篇）](https://zhuanlan.zhihu.com/p/390425571) 专业的雷电分析，作者雷电系列和拆解也不错。

有的显卡自带Type-c接口，比如RX 6800XT，[这里的兼容报告](https://www.sonnettech.com/product/amd-radeon-rx6800xt-bundles/techspecs.html)表明这种接口可以直接驱动 5k 显示器，但是显示器上面的USB设备比如摄像头无法识别。

### 雷电扩展坞
* HP雷电扩展坞 [HP Elite 65W Thunderbolt 3 Dock](https://support.hp.com/us-en/drivers/selfservice/hp-elite-thunderbolt-3-65w-dock/9822172)，如果接usb键盘鼠标，冷启动OpenCore中无法操作键盘鼠标，重启可以操作。这个扩展坞在 x86 Mac下面无法免驱，但是在 m1 Mac mini下面是免驱的。为什么 ultrafine 的usb键盘可以操作呢？usb 2.0 hub？ultrafine也不是所有情况都可以：只有windows关机和重启后才可以。如果是linux关机后开机，一样问题。上面的ASM1042A要到HP官网上才能下载驱动，Windows 默认没有带驱动，真是辣鸡啊，VGA 口也很碍眼，后来出了。
* Belkin Thunderbolt 3 Express Dock HD, F4U095，<https://www.belkin.com/th/support-article?articleNum=216428> 没有windows驱动和固件更新下载，据说Windows下面没法PD输出电流，买的有点唐突。
* Kensington Thunderbolt 4，4 个雷电 4 口，看上去不错，有点像交换机了。
* [CalDigit THUNDERBOLT 4 ELEMENT HUB](https://www.caldigit.com/zh/thunderbolt-4-element-hub-zh/)，四个雷电口+四个USB type A，更像hub而非扩展坞。如果有多个雷电显示器，这个很适合，其网页也很详细的介绍了兼容性，比如不支持双ultrafine 5k，但是Studio Display可以。如果我把它当成KVM来连接和控制多个PC电脑作为输入，这样避免雷电线的反复插拔，感觉不错。但是如果两台电脑同时开机，这样有两个雷电输入，这个hub会选择那个作为视频输入呢？同样方案的有 Kensington SD2600T Thunderbolt 4 Hub，也是4个雷电口。
* Type-C KVM，[这里](https://www.reddit.com/r/Thunderbolt/comments/fmpyv6/can_someone_recommend_me_a_thunderbolt_3_kvm/)讨论很热烈，其中“Type-C 8K Switch”似乎可行，价格不贵，Studio Display适用，但是Ultrafine 5k应该只能4k。
* [Sabrent Thunderbolt 4 KVM Switch](https://sabrent.com/products/sb-tb4k)两个雷电接不同主机作为输入，三个雷电输出。现阶段唯一的雷电 KVM 设备了。
* [CE-LINK U4-DOCK01](https://www.ce-link.com/productDetail/USB4%2012-in-1%20Docking%20Station%20with%20Daisy%20Chain/309)，很多大厂的都是CE-LINK OEM的，所以方案应该一样。千兆网卡是连 USB HUB 的，并非 PCIe 接口。好处是兼容性更好可以当成普通的 Type-C 扩展坞。用的芯片应该是 Goshen Ridge (JHL8440)，和主机端的Maple Ridge (JHL8540 and JHL8340) [不同](https://www.anandtech.com/show/16333/intel-maple-ridge-thunderbolt-4-controller-now-shipping)。已知的一个小问题是如果接在雷电卡后面启动的时候无法操作键盘进入BIOS。
* [Surface Thunderbolt 4 Dock](https://learn.microsoft.com/en-us/surface/surface-dock-whats-new)四雷电口，满配了，不过输入口是直接给的雷电线，这样不容易在不同设备间切换（因为有的设备比较远）。以太网口是USB 2.5G的，这样兼容一般的全功能TYPE-C口。

[120+ Thunderbolt 4 / USB4 Hubs & Docks compared](https://dancharblog.wordpress.com/2021/02/05/usb4-tb4-docks/)，很全面了。

### Linux
Fedora suspend 后 resume，雷电下的 USB 鼠标键盘无法工作，重新连接雷电设备也不起作用。B660m AORUS还有suspend后马上resume的问题，需要运行命令`echo GPP0 | sudo tee /proc/acpi/wakeup`来禁用[GPP0](https://forums.linuxmint.com/viewtopic.php?p=2117343)作为resume的事件触发源。Windows下面打开系统日志，查看类型为“Power Troubleshooter”的信息，也可以找到resume的事件触发源。

但在Windows 11上面休眠正常，而Fedora在冥王峡谷上面也休眠正常。似乎是Linux对于AMD平台电源管理支持还不够完善。B650M AORUS ELITE AX, BIOS F3h，在Linux 6.1.6内核下终于休眠正常。

显示器 Linux 下的亮度调节可以看[这里](https://www.feliciano.tech/blog/lg-ultrafine-brightness-linux/)，需要安装一个`ddcutil`的工具。其他平台参考 <https://www.chrisportela.com/garden/ddc/>。
```sh
ddcutil getvcp 0x10 # Get brightness. Ref https://www.ddcutil.com/monitor_notes/#lg-27md5kl-ultrafine
ddcutil setvcp 10 + 25 # Increase brightness
```

Linux 有个命令`boltctl`可以显示雷电设备连接状况。
```
 ● CE-LINK TB4-Dock01
   ├─ type:          peripheral
   ├─ name:          TB4-Dock01
   ├─ vendor:        CE-LINK
   ├─ uuid:          08aa8780-00f8-81e8-ffff-ffffffffffff
   ├─ generation:    USB4
   ├─ status:        authorized
   │  ├─ domain:     ef693804-91cd-ee10-ffff-ffffffffffff
   │  ├─ rx speed:   40 Gb/s = 2 lanes * 20 Gb/s
   │  ├─ tx speed:   40 Gb/s = 2 lanes * 20 Gb/s
   │  └─ authflags:  boot
   ├─ authorized:    2023年12月16日 星期六 03时12分19秒
   ├─ connected:     2023年12月16日 星期六 03时12分19秒
   └─ stored:        2023年12月12日 星期二 09时48分51秒
      ├─ policy:     auto
      └─ key:        no
```

### Mac
苹果自身设备兼容性理应是最好的。

Mac Mini M2，显示器和 Belkin 扩展坞没有任何问题，毕竟这个扩展坞本来就是为苹果用户设计的。而且Mini休眠后也可以用键盘鼠标唤醒，这个就很厉害了。另外：Mini休眠时指示灯也是亮的，如何判断是否休眠？我能想到的办法只有耳朵凑到机器后面的风扇口听听有没有风声。联想到MacOS电源设置里面也没有过期休眠的时间，似乎苹果想让用户忘掉这个设备的电源管理方式：一直放在那里就好了。

### 各种奇怪的问题
* 屏幕撕裂/错位，有台机器在Windows下容易在屏幕中间出现细小的错位，但是同样机器在Linux就没有这个问题。使用`LG Screen Manager`更新显示器固件似乎缓解了下这个问题。
* 热拔插不稳定，容易出现屏幕能点亮但是hub下面的键盘鼠标无法连接。所以尽量在关机或者待机后再拔插雷电线。

### 总结
总的看来，[雷电](https://zh.wikipedia.org/wiki/Thunderbolt)的理念确实不错：一根线能传输电源和多种不同数字信号，这对桌面的整洁至关重要，特别适合某些场景：

1. 笔记本接显示器工作模式，可以说雷电的最早就是为 MacBook 设计的。
2. 多个台式机共享显示器。一般用 KVM 切换来解决这个问题，但是如果接高分辨率显示器，KVM 价格就很贵了。

可惜的是，因为是 Intel 和 Apple 合作的方案，其他系统和平台存在不少兼容性问题。
