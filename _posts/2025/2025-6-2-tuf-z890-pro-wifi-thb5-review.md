---
layout: post
title: "TUF GAMING Z890-PRO WIFI & 雷电 5 评测"
date: 2025-6-2
categories:
  - DIY
typora-root-url: ../../../blog
---

# TUF GAMING Z890-PRO WIFI

LGA 1851散热器和1700兼容的。我的老利民单塔安装太麻烦，有点水，固定不好弄，手册差。1700现在的CPU是纵向的，散热和现在的差别很大，看了下，双塔的可能会好点。

B650E USB4 上面的 Windows 启动盘没法用，需要重新安装。Linux没有问题，不需要任何配置，默认配置就使用很好，包括suspend/resume。resume 也可以用鼠标键盘唤醒。BIOS Version 1206。

机器重启风扇声音很小，没有技嘉的那种猛然的一大声。

开机 uefi 界面的小小的 tuf gaming logo 难看，没雕牌好看。

主板没有开关机按钮，没有f-panel 辅助接口，感觉比b650e usb4掉了个档次啊。

两个雷电口还是只有两个DP输出，Windows USB4 里面也是只有一个控制器。和我的技嘉B6560E USB4一样。

内存兼容性还不如B650，国产颗粒点不亮。是因为刚出来么？

[ASUS + Kingston](https://edgeup.asus.com/2025/aemp-iii-gives-you-a-seamless-experience-with-the-new-kingston-64gb-ddr5-memory-modules/) 64GB x4, KVR64A52BD8-64，CUDIMM内存，绿条，价格不会很贵吧。

Windows 下面一对PCI未安装驱动，看着烦心，虽然使用似乎也没啥问题。在华硕网站一个个安装驱动后解决，不用安装600MB的Intel Management Engine Interface driver。

Windows 下面待机并且屏幕关闭的时候（并没有suspend），移动鼠标会有设备连接的声音，事件有 kernel-power 系统会话已从 7 转换为 9（数字是不断增长的），原因 InputHid。似乎为了省电机器全部suspend前陆续关闭了某些设备。分阶段待机？这样更为省电。

boltctl output as below. Note the policy and key value.

```
 ● Belkin International, Inc. Thunderbolt 3 Dock - Pro
   ├─ type:          peripheral
   ├─ name:          Thunderbolt 3 Dock - Pro
   ├─ vendor:        Belkin International, Inc.
   ├─ uuid:          c6030000-00a2-ac0e-036f-38835eb19001
   ├─ generation:    Thunderbolt 3
   ├─ status:        authorized
   │  ├─ domain:     faab8780-009d-62e3-ffff-ffffffffffff
   │  ├─ rx speed:   40 Gb/s = 2 lanes * 20 Gb/s
   │  ├─ tx speed:   40 Gb/s = 2 lanes * 20 Gb/s
   │  └─ authflags:  none
   ├─ authorized:    Fri 03 Jan 2025 04:36:14 PM UTC
   ├─ connected:     Fri 03 Jan 2025 04:36:12 PM UTC
   └─ stored:        Mon 21 Oct 2024 12:45:59 AM UTC
      ├─ policy:     auto
      └─ key:        no
      
 ● LG Electronics UltraFine 5K
   ├─ type:          peripheral
   ├─ name:          UltraFine 5K
   ├─ vendor:        LG Electronics
   ├─ uuid:          c3030000-0062-6c1e-036b-618ad8a17202
   ├─ generation:    Thunderbolt 3
   ├─ status:        authorized
   │  ├─ domain:     faab8780-009d-62e3-ffff-ffffffffffff
   │  ├─ rx speed:   40 Gb/s = 2 lanes * 20 Gb/s
   │  ├─ tx speed:   40 Gb/s = 2 lanes * 20 Gb/s
   │  └─ authflags:  none
   ├─ authorized:    Fri 03 Jan 2025 04:36:14 PM UTC
   ├─ connected:     Fri 03 Jan 2025 04:36:14 PM UTC
   └─ stored:        Wed 10 Jul 2024 04:14:59 AM UTC
      ├─ policy:     auto
      └─ key:        no
```

### Asus USB4 Card

如果同时集显HDMI输出到便携屏幕，Windows会马上重启，Linux下面只有便携显示器点亮。很奇怪的问题。进去Windows后，还是只有一个USB4主机路由器。总的感觉兼容性比较差，很容易重启。我已经升级到2024最新固件，注意他固件分不同主板，还有专门为PD的固件。接NVIDIA显卡，Windows下面桌面有5k，活动信号(active signal resolution)只有 4k，

![image.png](/images/2025/tuf-z890/image.png)

![image.png](/images/2025/tuf-z890/image%201.png)

从上面看，接到是第二个显示器，而且插入另外雷电口显示为第一个显示器。所以每个口只有一个DP信号输出？我的B650E USB4同样ASM4242似乎没有这个问题，而且windows显示有5k分辨率，所以是nvidia驱动问题？但是Fedora下面显示为正常的5K，奇怪。没有，windows 5k是前面集显残留的配置，选一个其他分辨率后5k选项就消失了。上面截图里面如果选择两个显示器，机器马上重启。

![image.png](/images/2025/tuf-z890/image%202.png)

似乎显卡认为另外一个显示器不是活动的。

不知道是nvidia显卡问题还是usb4 card问题，我感觉还是card问题，因为记得nvidia显卡通用性很强。系统进linux就直接黑屏，我没有装专门的驱动。安装nvidia open driver后，asus usb4雷电口可以显示，wayland正常，同样suspend后没法resume。

这个卡我后来 700 卖了，一则有了雷电5的卡，再则好像MSI USB4的卡是兼容的。

![image.png](/images/2025/tuf-z890/image%209.png)

![image.png](/images/2025/tuf-z890/image%2010.png)

看上去并不兼容啊。

### Asus ThunderboltEX5

[home page](https://www.asus.com/motherboards-components/motherboards/accessories/thunderboltex-5/). [ASUS ThunderboltEX 5 擴充卡效能簡測](https://www.mobile01.com/topicdetail.php?f=495&t=7045632) 雷电 5，注意有4种芯片，9480只能做SSD，pcie不能驱动显卡。

[【老湿基】雷电5 的一切信息](https://www.bilibili.com/video/BV13P1FYgEih/)

可能卡刚出，兼容性挺差的。接5k显示器bios界面无法显示，等长久后可以进windows，还是和上面一样4k分辨率，应该是windows驱动的问题。

模式如果选择intel v2兼容性比usb4要好点，接我的雷电3硬盘盒能到40g，usb4模式只能到20g。

其实这个速度对于我来说并没有啥意义吧。更多的希望这个卡能带动更多的显示器，这个卡有3个DP IN。

![image.png](/images/2025/tuf-z890/image%203.png)

所以应该两个type-c都能接到显示器上面。如果一根雷电线，用Dell 6k菊链后面能否带动LG 5k？虽然Dell 6k都是雷电4规格。如果用扩展坞，不需要菊花链，应该容易实现。

雷电4扩展坞，接主板雷电口，Nvidia显卡，DP IN 2，DP IN不可用0。先接5K显示器，这个就是一般使用模式，可以点亮，然后2k屏幕接扩展坞，显示无信号。因为DP已经用完。

换成3 dp rx6600显卡，2k屏幕接雷电扩展卡，可以点亮。隧道DP IN2，DP IN不可用1，因为这个DP直接给了便携2k屏幕。所以我的雷电4扩展坞只能有2个DP，这个和[USB4 ELEMENT HUB](https://www.caldigit.com/thunderbolt-4-element-hub/)一样，虽然后者有3个雷电下行，还是只能支持两个屏幕。这说明雷电 5 扩展坞还是有可能点亮我的 lg5k + 2k 屏幕的。然后这里的RX6600带lg显卡也有点奇怪，虽然有5k分辨率，但是 Windows 显示那里“推荐”为4k，而且活动信号也为4k，这个和 Nvidia 显卡一样，但是屏幕很清晰，似乎确实为5k。正常情况下是没有“推荐”的字样的。

说明书里面有“使用單一 Thunderbolt 顯示屏時，請確認使用 mini DisplayPort 的 MINI DP IN_1”

[SD5800T](https://www.kensington.com/p/products/device-docking-connectivity-products/laptop-docks-usb-accessories/sd5800t-thunderbolt-4-and-usb4-quad-video-docking-station-98w-pd-dphdmi-windowsmacos/?srsltid=AfmBOoq6I2e9NY2IKJirE4LENkGQoQ6Wi8Q3OFRwoIEKQ45eHixbJvGb) 这个可以接4个4K显示器，不知道如何做到。[jd link](https://item.jd.com/100100521994.html)。

> “如果您打算连接 4K 60Hz 以上的双显示器，例如双 5K 60Hz 或 6K 60Hz，您的显示器和主机 GPU 必须支持 DSC 1.2（显示流压缩）和 DP 1.4 HBR3。 Apple M1、M1 Pro、M1 Max 和 M1 Ultra 支持 DSC 1.2 和 DP 1.4 HBR3。请注意，LG UltraFine 5K 显示器不支持DSC。” [https://www.caldigit.com/zh/thunderbolt-station-4-zh/](https://www.caldigit.com/zh/thunderbolt-station-4-zh/)
> 

所以雷电 4 HUB没法隧道 3 跟 DP 信号了？菊花链感觉就更不可能了。因为雷电 5 和 4 的一个对比是：

- Dual 4K 60Hz monitors, 8K monitor at 30Hz
- Three 4K 144Hz monitors, 8K monitor at 60Hz

![image.png](/images/2025/tuf-z890/image%204.png)

![image.png](/images/2025/tuf-z890/image%205.png)

正常工作状态，直接接主板雷电口，从上面可以看到确实有两个DP信号。

![image.png](/images/2025/tuf-z890/image%206.png)

雷电5，虽然最后thunderbolt 3虽然有两个dp in，也确实有两个一大一小显示器，但是带宽不够，不足以驱动两个4k。我才这是因为带宽分配的时候觉得一个大的DP就够了，动态分配的时候没有平均分配两个。

UGREEN hub connects Surface laptop. 5K is OK. So root cause is thb card.

When 1) connect DP to DP_1 of card & 2) only 2 DP IN to prevent confusion, situation is better: BIOS is ok. But still 4K (only 1 display).

### 雷电5 扩展坞

- 绿联雷电5多功能扩展坞U715, offical [desc](https://www.lulian.cn/product/1224.html), 4 thb5 ports,1699￥
- [OWC Thunderbolt 5 Hub](https://www.owc.com/solutions/thunderbolt-5-hub), just a hub
- 肯辛通 SD5000T5, only for laptop, 3k￥
- [CalDigit Element 5 Hub](https://www.caldigit.com/thunderbolt-5-element-5-hub/)，“3 Monitors on Windows”里面规格写的很详细，“Dual 5K monitors must be Thunderbolt 4 or Apple Studio Displays. Dual LG UltraFine 5K monitors are not supported. ”还是不行？单个LG 5k+Studio Display可以吧？

[https://www.intel.com/content/www/us/en/products/sku/225919/intel-jhl9480-thunderbolt-5-accessory-controller/specifications.html](https://www.intel.com/content/www/us/en/products/sku/225919/intel-jhl9480-thunderbolt-5-accessory-controller/specifications.html) JHL9480 绿联用的也是这个，“2 DP Sink, 3 DP SRC”这个如何解释？现在确实hub确实可以接三个显示器，但是两个DP Sink如何转出3个DP？ChatGpt说“The JHL9480 may employ MST hubs (Multi-Stream Transport), which allow one DP stream to be split into multiple virtual streams for multiple displays.” Windows dev mode page shows there are 2 DP IN. So thb 5 card can’t merge 2 DP into 1 since it wants to support 3 displays?

### 绿联雷电5多功能扩展坞

雷电3，Titan Ridge v2, b650m，机器无法启动，亮vga错误红灯。

雷电5, Asus ThunderboltEX5，没有开机画面（拔掉键盘会显示bios，奇怪，快速启动？），进入windows后能点亮屏幕。但是lg 5k只能4k显示，显卡分辨率里面只有4k。奇怪的是这时候隧道DP IN为2，hub再接个便携显示器，能点亮，隧道DP IN为3，这说明lg 5k的时候确实输出了两条DP。结合前面的直接连控制器的测试，感觉雷电5控制器/转接卡的兼容性问题大点。如果安装完整版本AMD Software Adrenalin Edition，可以看到会多了一个LG显示器。Display里面会显示带宽current link settings，一个为5.4 Gbps x4，一个为1.62Gbps x1。但是我在b650m下面只会看到一个显示器，带宽为5.4 Gbps x4。

### when B650m thb3 is ok

![image.png](/images/2025/tuf-z890/image%207.png)

![image.png](/images/2025/tuf-z890/image%208.png)

tuf z980, intel thb port
```
- USB-USB4DeviceRouter-EventLogs
    - <Event xmlns="http://schemas.microsoft.com/win/2004/08/events/event">
    - <System>
    <Provider Name="Microsoft-Windows-USB-USB4DeviceRouter-EventLogs" Guid="{d07e8c3f-78fb-4c22-b77c-2203d00bfdf3}" />
    <EventID>1</EventID>
    <Version>1</Version>
    <Level>4</Level>
    <Task>1</Task>
    <Opcode>0</Opcode>
    <Keywords>0x8000000000000001</Keywords>
    <TimeCreated SystemTime="2025-02-16T23:20:05.0560915Z" />
    <EventRecordID>42095</EventRecordID>
    <Correlation ActivityID="{85a097cd-8037-0000-8412-ec853780db01}" />
    <Execution ProcessID="4" ThreadID="7936" />
    <Channel>System</Channel>
    <Computer>tuf265</Computer>
    <Security />
    </System>
    - <EventData>
    <Data Name="fid_DomainId">5046273</Data>
    <Data Name="fid_DpOutRouterTopologyId">3</Data>
    <Data Name="fid_DpOutRouterTopologyId">5</Data>
    <Data Name="fid_DpOutRouterTopologyId">0</Data>
    <Data Name="fid_DpOutRouterTopologyId">0</Data>
    <Data Name="fid_DpOutRouterTopologyId">0</Data>
    <Data Name="fid_DpOutRouterTopologyId">0</Data>
    <Data Name="fid_DpOutRouterTopologyId">0</Data>
    <Data Name="fid_DpcdCmId">0</Data>
    <Data Name="fid_DpOutAdapterNumber">11</Data>
    <Data Name="fid_DpInRouterDepth">0</Data>
    <Data Name="fid_DpInAdapterNumber">5</Data>
    <Data Name="fid_IsRebuilding">true</Data>
    <Data Name="fid_Attempts">1</Data>
    <Data Name="fid_DpOutLocalCapabilities">12624692</Data>
    <Data Name="fid_DpInLocalCapabilities">364946228</Data>
    <Data Name="fid_MaskedRemoteCapabilities">146842164</Data>
    <Data Name="fid_DpRxPollingDuration">652939</Data>
    <Data Name="fid_CommonCapabilities">2294325780</Data>
    <Data Name="fid_BandwidthUsed">0</Data>
    <Data Name="fid_DpBwAllocationModeSupported">true</Data>
    <Data Name="fid_DpBwAllocationModeEnabled">true</Data>
    </EventData>
    </Event>
- USB-USB4DeviceRouter-EventLogs
    - <Event xmlns="[http://schemas.microsoft.com/win/2004/08/events/event](http://schemas.microsoft.com/win/2004/08/events/event)">
    - <System>
    <Provider Name="Microsoft-Windows-USB-USB4DeviceRouter-EventLogs" Guid="{d07e8c3f-78fb-4c22-b77c-2203d00bfdf3}" />
    <EventID>1</EventID>
    <Version>1</Version>
    <Level>4</Level>
    <Task>1</Task>
    <Opcode>0</Opcode>
    <Keywords>0x8000000000000001</Keywords>
    <TimeCreated SystemTime="2025-02-16T23:20:05.0559271Z" />
    <EventRecordID>42094</EventRecordID>
    <Correlation ActivityID="{85a097cd-8037-0000-8312-ec853780db01}" />
    <Execution ProcessID="4" ThreadID="4088" />
    <Channel>System</Channel>
    <Computer>tuf265</Computer>
    <Security />
    </System>
    - <EventData>
    <Data Name="fid_DomainId">5046273</Data>
    <Data Name="fid_DpOutRouterTopologyId">3</Data>
    <Data Name="fid_DpOutRouterTopologyId">5</Data>
    <Data Name="fid_DpOutRouterTopologyId">0</Data>
    <Data Name="fid_DpOutRouterTopologyId">0</Data>
    <Data Name="fid_DpOutRouterTopologyId">0</Data>
    <Data Name="fid_DpOutRouterTopologyId">0</Data>
    <Data Name="fid_DpOutRouterTopologyId">0</Data>
    <Data Name="fid_DpcdCmId">0</Data>
    <Data Name="fid_DpOutAdapterNumber">10</Data>
    <Data Name="fid_DpInRouterDepth">0</Data>
    <Data Name="fid_DpInAdapterNumber">6</Data>
    <Data Name="fid_IsRebuilding">true</Data>
    <Data Name="fid_Attempts">1</Data>
    <Data Name="fid_DpOutLocalCapabilities">12624692</Data>
    <Data Name="fid_DpInLocalCapabilities">364946228</Data>
    <Data Name="fid_MaskedRemoteCapabilities">146842164</Data>
    <Data Name="fid_DpRxPollingDuration">704734</Data>
    <Data Name="fid_CommonCapabilities">2294325780</Data>
    <Data Name="fid_BandwidthUsed">0</Data>
    <Data Name="fid_DpBwAllocationModeSupported">true</Data>
    <Data Name="fid_DpBwAllocationModeEnabled">true</Data>
    </EventData>
    </Event>
```
