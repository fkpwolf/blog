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
- 可以支持更多 scale 倍数
- 不同屏幕可以用不同 DPI
- 窗口拖动的时候可以跨屏显示，Mac 会截掉一半

Windows 下面 250% 的放大比例最为完美。Gnome 下面 200% 放大比在 4k 下表现好很多，4k 下 200% 放大会让窗口组件过大而显得不够精细， 而 [Linux Fractional Scaling](https://wiki.archlinux.org/title/HiDPI#Fractional_scaling) 还不成熟。

### 主板
- [MEG Z590I UNIFY](https://www.msi.com/Motherboard/MEG-Z590I-UNIFY) 有两个 DP IN 接口，3K 太贵了，特别是只支持 11 代CPU，不支持 MacOS。但是两个 DP IN 算是最高规格了，[BIOS](https://www.chiphell.com/portal.php?mod=view&aid=25543&page=4) 里面也有雷电相关的详细配置。[MEG Z590 ACE](https://www.msi.com/Motherboard/MEG-Z590-ACE)也有两个 DP IN。
- [ProArt B550-Creator](https://www.asus.com/Motherboards-Components/Motherboards/ProArt/ProArt-B550-CREATOR/), one DP-IN. [BIOS](https://dlcdnets.asus.com/pub/ASUS/misc/Manual/PRIME_TUF_GAMING_B550_Series_BIOS_EM_WEB_EN.pdf)里面雷电选项还挺丰富。"PCIe 3.0 x4 will be switched from Thunderbolt™ 4 to PCIEX16_3 if the slot is populated; by then Thunderbolt™ 4 will have no output" Z490也是这样，因为没有足够的PCI条数。
- B550 VISION D-P all has one DP-IN. 2k. But not mATX board. Manual doesn't show thunderbolt menu in detail - same as B550M AORUS. tonymacx86 details guide on this board.
- ASRock X570 Phantom Gaming-ITX/TB3 amazon.cn 1700¥. BIOS Manual has no thunderbolt info. 似乎一个DP IN只能支持最高4K显示。
- PRIME X299-DELUXE II, 技嘉 X299X AORUS Designare都有两个dp in，两个雷电口。就是板子太贵，平台有点老，cpu 似乎还可以，7940x/7960x/7980xe 价格都不贵。
- Z490主板里面支持 2 DP in 的似乎只有华硕 PROART Z490-CREATOR 10G，而且 BIOS 里面的功能也很全。完整的OpenCore文件，似乎已经被研究的很透彻了。可惜规格这么高的z490大板只有少的可怜的pci通道&CPU太老，真实使用起来和我的 B460m 差不多吧。
- 10代Intel cpu中 10850k 比较有性价比，10核心20线程，2400¥。
- 超微X11SRA有雷电header，但是BIOS不支持，这么多PCI-E，真是可惜了。这里说可以在没有header的超微X11DPH-T上面实验成功，奇怪了，多半是默认开启，但是pre-boot肯定是没有了。
- Pro WS W480-ACE，两DP IN，支持CPU有10代，11代和Xeon W，不过PCI条数似乎没啥亮点

### 雷电扩展卡
https://www.gigabyte.com/Motherboard/GC-TITAN-RIDGE-rev-20#kf https://www.gigabyte.com/Motherboard/GC-TITAN-RIDGE-rev-10#kf
居然连AMD B550也支持，B550M AORUS PRO感觉这个还可以，DP接口支持5K，如果配个APU就可以省下显卡钱了。
https://www.chiphell.com/thread-1955259-1-1.html TITAN RIDGE 不需要雷电header就可以，有时候需要短接。
Z490支持DP IN的，也就是支持5k输出的是Z490 VISION D。
https://cn.msi.com/Motherboard/Z490M-S1/Specification 这个主板规格比较完美，不过是微星的，技嘉的扩展卡不知道是否兼容。或者MAG B460M MORTAR 迫击炮感觉也行，有雷电口5pin JTBT1，这个OC支持的更多 https://heipg.cn/tutorial/b460m-install-big-sur.html。好玩是的从规格书上看是微星这个板子有两个thunderbolt接口:
![](/images/2021/thunderbolt-port.png)
GC-TITAN-RIDGE-20的规格书也有这两个接口。Z490/B560M AORUS PRO AX技嘉的板子也有两个雷电header，B550M AORUS PRO则只有一个。问题是技嘉的扩展卡和主板都没有详细列出header的定义，所以不确定微星的主板是否和技嘉的扩展卡兼容。这个 RTD3 好像是“现代待机”（Modern Standby）的东西。

技嘉Titan ridge v1.0使用：固件要升级，否则无法视频输出。主板没有thunderbolt header，1//3要短接，USB 接口要连接，否则难以激活。
HP Elite 90W Thunderbolt 3 Dock，这个也要更新固件，否则接在Ultrafine上面的USB鼠标键盘容易卡顿，和固件没有关系。现在这个Dock的唯一问题是接在Ultrafine上面的usb键盘无法进入BIOS。我在thunderbolt 控制中心看到安全性级别是“用户身份验证(SL1)”，我的冥王峡谷能进是因为安全级别比这个低么？

确实，我修改NUC BIOS后为“无安全性(SL0)”，但是我也没法在技嘉主板BIOS里面修改啊。https://github.com/ameyrupji/thunderbolt-macpro-5-1/blob/master/GC-TitanRidge.md 这里有刷机的步骤。后来发现这个技嘉板子里面可以修改，修改后进入thunderbolt controller center也能看到security level改变了，我改为none。但是启动后发现雷电下面usb无法链接，重启也不行，只有热拔插雷电线可以，只能改回默认的 user auth。似乎这个如果bios里面修改了值，bios启动时会修改雷电扩展卡配置，但是不知道是因为和主板不兼容，还是我没有连接header，导致启动后OS虽然能看到修改后的安全级别，但是功能出现问题。这个是我的猜测。
B550M AORUS BIOS 没有pre-boot acl支持怎么办？可以试试去掉mortar的雷电header改为短接，如果pre-boot还是可以工作，那说明这个功能完全是由bios控制。
用AMIBCP看了b550m vision d板子的Bios，选项也很少。不是说技嘉的雷电支持是最好的么？
这个板子问题也挺多：
1. 关机变成重启，我再BIOS里面关掉了ethernet weak on lan，可以关机，但是在windows日志里面还是有意外关机的错误
2. “跟这台计算机连接的一个USB设备运行不正常“，导致ultrafine下级的usb无法工作。有的地方说”出现该问题只有两个原因：一是数据线问题，二是主机电压问题。“ 真是麻烦啊。
3. 换回titan ridge v1.0后在user auth安全模式下似乎稳定了些。错觉
4. 关掉windows的快速启动，在user auth模式下似乎很稳定了。no auth还是老问题。
5. 使用"测试架PCB夹子治具夹具探针"接上thb 5pin口，问题依然，和短接没区别，只是可以正常关机了。看来要pre-boot 键盘支持的话必须bios支持
6. user auth下，发现关机后无法连接，后来还是必须关掉windows的快速启动

### MAG B460M MORTAR
v11 bios菜单里面有雷电，虽然问题多多（雷电设备能看到，但是USB和PCI网卡都看不到）。但是v14居然去掉了。v13 changelog 我看还有改进 Thunderbolt functional patch. 换成v13，问题解决。
security level我如果改成none，和上面技嘉B550m一样的问题：usb 无法连接。有时候第一次可以，但是重启后就不行了。改成user auth好很多，但这样和上面其实效果一样。所以关键还是在这个雷电扩展卡？这个板子bios里面雷电选项很多，比如从雷电启动。
![](/images/2021/MSI_board_thunderbolt.png)
这里设置pre-boot ACL就可以用接在雷电上面的鼠标键盘进入BIOS了，不管这个键盘是接在一级还是二级雷电设备下。微星的这个BIOS太强了，还是 intel 做的好？这里看MSI MEG Z590I Unify也是支持pre-boot ACL的了。
为什么冥王峡谷反而没有？这里说这个版本的驱动是支持的啊：
```
Added support for PRE-BOOT ACL feature (deprecating previous implementation from 17.1.64.250): Devices that can be used for boot or during pre-boot need to be pre-approved. Pre-Boot ACL solution enables device pre boot approval by maintaining an access control list in the FW as well
```
但是nuc bios里面没有看到对应的配置。看看其thunderbolt nvm version is just 33.0，但是 NUC 官网也没有新的版本。
我猜测进入windows系统后动态的启用和批准设备的pre boot，这样重启后启动就能在启动阶段使用键盘。
其实后来测试这个NUC对pre-boot支持的已经挺好了，虽然bios里面没有，默认似乎是已经开启了。

### 雷电 4
https://www.gigabyte.com/Motherboard/GC-MAPLE-RIDGE-rev-10#kf 这个表明支持5k，我看也是Display Port 1.4。这个也可以。https://www.msi.com/Motherboard/MAG-B560M-MORTAR/Specification 微星这个性价比的选择1x TBT connector (Supports RTD3) 。这个B560M AORUS PRO AX白雕也挺好看。
Asus Thunderbolt EX3-TR，新版本，居然不支持mATX的板子。14-1 pin Thunderbolt，这个只有asus板子才有这个规格吧。
Kensington Thunderbolt 4 有 4 个雷电口，看上去不错，￥2400。

### 显卡
https://superuser.com/questions/845302/what-specs-must-a-graphics-card-have-to-power-a-5k-monitor
https://images.nvidia.com/aem-dam/en-zz/Solutions/design-visualization/quadro-product-literature/NV-DS-NVS-310-May12-US-NV.pdf
https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/quadro-product-literature/NVS_510_DS_Oct12_NVIDIA_LR.pdf
都支持MST，但是记得安装最新驱动。安装后直接显示一个5k显示器，否则会显示两个屏幕。

以前是ultrafine的声音设备只有先进入windows，然后重启进入macOS后，macos可以认识声音设备，但是刷bios后这个问题消失了。
似乎是pre-boot ACL问题，但是我这个应该要linux和macos自身的问题，也就是关机时设置雷电外设。
https://fedoramagazine.org/thunderbolt-how-to-use-keyboard-during-boot-time/

如果type-c或者雷电线支持视频，USB和声音传输，这就有点像KVM功能了。

发现一个WIN PC使用LG Ultrafine显示器的神器 https://www.chiphell.com/thread-2324414-1-1.html ，200￥，不知道是否支持5k

对于5k，此显示器同时支持MST和SST。用雷电卡，将双路DP1.2合并到USB-C形状的雷电，直接MST方式驱动即可；另外新款N卡，支持DP1.4输出即采用SST方式驱动，因WIN10同时支持MST和SST，所以两种方式很容易驱动。但是HACKINTOSH驱动5k只能采取MST方式，系统原生不支持SST驱动5k，所以只能采取win10的第一种方式，即利用雷电卡将两路DP1.2合并到USB-C形状的雷电，驱动显示器。
https://www.chiphell.com/forum.php?mod=redirect&goto=findpost&ptid=2327490&pid=47599622 贝尔金CV10 线(DP转 TYPEC) 据说可以直接搞定。不过不是雷电方案，要拖几根usb线，感觉违背了雷电初衷。

### 雷电扩展坞
OWC Thunderbolt 3 Pro 配有 10 Gb 以太网连接
startech.com thunderbolt 3 DOCK 感觉就这个便宜点
HP雷电扩展坞 HP Elite 65W Thunderbolt 3 Dock，如果接usb键盘鼠标，冷启动OpenCore中无法操作键盘鼠标，重启可以操作。
为什么 ultrafine 的usb键盘可以操作呢？usb 2.0 hub？ultrafine也不是所有情况都可以：只有windows关机和重启后才可以。如果是linux关机后开机，一样问题。
上面的ASM1042A要到HP官网上才能下载驱动，Windows 默认没有带驱动。真是辣鸡啊。（默认似乎有驱动，但是安装titan ridge后没法驱动了）

Belkin Thunderbolt 3 Express Dock HD, F4U095
https://www.belkin.com/th/support-article?articleNum=216428 没有windows驱动和固件更新下载
据说Windows下面没法PD输出电流，买的有点唐突

### 总结
总的看来，雷电的理念确实不错：一根线能传输电和数字信号，这对桌面的整洁至关重要，特别适合某些场景：
1. 笔记本接显示器工作模式
2. 多个台式机共享显示器。一般用 KVM 切换来解决这个问题，但是如果接高分辨率显示器，
可惜的是，因为是 Intel 的方案，存在不少兼容性问题。