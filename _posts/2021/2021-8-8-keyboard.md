---
layout: post
title: "客制化键盘"
date: 2021-8-8
categories:
  - DIY
typora-root-url: ../../../blog
---

### 键帽
* Cherry G80-3000 白色键盘的键帽是pbt，黑色的键盘帽是pom的
* 关于键帽表面材质以及印刷工艺的详细介绍 <https://www.wstx.com/p-20810-1>
* 现在虽然流行PBT，但是一般PBT虽然不容易打油，更“干燥”，但是感觉粗糙。
* Filco 圣手87，虽然是ABS键帽，但是比PBT的那种磨砂感更为细腻。不过这在这种很少了。ABS 缺点是更容易打油，但是更容易做出不同色彩和配色的键帽。高端用的都是双色ABS。

### 轴体
以是否有段落感区分。如果按下去有明显的咔嚓一声来确认触发，这个就是段落轴。
* TTC 快银轴，无段落，比较热门的轴体。
* 佳达隆 G 黄轴，无段落，手感柔弱，但压到底后回弹有力，性价比高。
* Box Navy 段落轴，挺硬的
* Romer-G，罗技和欧姆龙合作出品，双触点，轴柱特殊导致很难换键帽

### PCB
多模支持多种模式，比如有线、2.4G无线和蓝牙无线。大多数是一个单片机加上蓝牙模块来支持多模，这对蓝牙模块 MCU 其实是浪费。
* 有线如果支持键线分离和USB Type-c，使用起来也没不方便。以前没有分离模式下，线预留的比较长，感觉桌面很凌乱。
* 2.4G 需要额外适配器，但是兼容性会更好，能操作BIOS，这对多系统很重要
* 蓝牙因为是标准，适用更广泛，新的BLE标准能节省键盘电量。但是需要有个配对过程，大多数蓝牙键盘都可以同时适配多个设备，这样切换时就不需要重新配对。
对于热拔插键盘，PCB会包含热拔插轴座，否则需要手工直接焊接轴体到PCB上面。

### 其他
除了上面基本部件，还需定位板和外壳。
* 定位板是用来防止轴体晃动的部件，有各种材质：铝、黄铜、钢板、玻纤。铜外观更具质感，铝更轻。
* 外壳则是包含上面所有部件的外壳。常见的有 GH60 通用外壳，个人比较喜欢黑透款式，塑料材质，这样整块键盘可以轻松拿起，有樱桃原厂无钢板键盘的风格。

### 配列
所谓配列，就是键盘布局。包含数字小键盘的全尺寸的是104布局，这个最常见。去掉数字键盘但是包含F1-F12功能键的是87布局，这个算是成品键盘里面最常见了，其中IKBC是个不错的选择。
去掉F1-F12功能键和方向键就是60布局了，这个在客制化键盘里面最常见。去掉F1-F12功能键一般人还能适应，因为用的不多。但是去掉了方向键而改用组合键来实现上下左右，这个挺多人不适应。
所以就有了加上方向键的64布局。但64键导致Right Shift键变小，这样特殊字符就容易误触。

现在又流行75配列：在普通60布局上面加上了F1-F12功能键，大Right Shift，还多了个旋钮按键。对于我来说，F1-F12功能键多余。更大的改变是PCB和外壳、定位板不是通用的，所以一般作为套装来卖，后期更换余地不大，这有违客制化的精神。如果去掉F1-F12，则变成67/68布局，也是大Right Shift，对于我来说很完美了。

### 开源键盘
* SMK68: First Community-Driven OpenSource RISC-V Custom Mechanical Keyboard~ with BL706. With Programmable OLED Keys, RGB Led, Hot-swappable, USB/BLE Dual-Mode, vibration feedback, and even Serial Terminal ! <https://github.com/sipeed/sipeed_keyboard>
* <https://github.com/Lotlab/Lot60-BLE-Keyboard>  全开源，<https://wiki.lotlab.org/index.html>
* [Makerdiary M60](https://makerdiary.com/pages/m60-mechanical-keyboard)，我买的第一个热拔插键盘
* System76 Launch Configurable Keyboard <https://github.com/system76/launch>，Launch Lite 则是 65% 键盘

### 开源软件
* QMK <https://qmk.fm/> 大多数有线键盘都基于这个方案，支持挺多MCU，但是缺少对无线/蓝牙的支持。
* ZMK <https://zmk.dev/> ZMK Firmware is an open source (MIT) keyboard firmware built on the Zephyr™ Project Real Time Operating System (RTOS). Wireless First.
* VIA <https://caniusevia.com/> 修改键盘配列的一个图形界面软件。这个软件强的地方在于修改后立即生效，没有保存的动作。而且下次修改的时候会加载键盘里面的配置，所以是无缝连接。如果能让一款成品键盘，比如IKBC的支持VIA，那是极好的。

为什么要改标准布局的键盘的布局呢？因为每个人键盘使用和常用按键都不同，对于我来说，最迫切需求是：
1. 能够快速让电脑待机
2. 声音控制

### DIY无线优联机械键盘
这种方式是拿罗技的主控板，加上定制PCB来实现键盘。因为罗技的无线技术还是很牛的：省电、稳定，这两点对于无线键盘来说都很重要。
* [[心血改装] 全球首发，DIY了史上第一把可编程无线优联40%机械键盘](https://www.chiphell.com/thread-1499401-1-1.html )
* [[心血改装] 优联40%迷你无线机械键盘——YD40_Wireless诞生记](https://www.chiphell.com/thread-1560853-1-1.html )
* [DIY Keyboard with Logitech Unified Receiver](https://geekhack.org/index.php?topic=58848.0)
他这个得有个优联主控，多半是从已有的键盘上面拆下来的。然后PC端也有个把优联接受装置转为普通USB的转换器，这个转换做的是把原来主控对应键盘的布局转为现在键盘的布局。 注意这个电路板有很多线连接到各个键盘，一般的 MCU 可能没有这么多的针脚/GPIO。

### 小键盘 keypad
一般多媒体键盘都包含音量控制键，很方便控制音量大小。而且对于我来说，PC上有Windows+Linux，使用蓝牙键盘后，开机时无法连接而进入BIOS里选择启动操作系统。
* <https://www.cnx-software.com/2021/08/07/arduino-powered-5-key-keypad-rotary-encoder/>  小键盘，包含旋钮。感觉挺好，不过有没有2.4G 无线的？旋钮是“EC11编码开关”
* <https://www.linchenguang.com/2020/10/23/Stroll-keyboard-fan-community-Selection/>
* <https://github.com/mattdibi/redox-w-firmware>
* <https://www.youtube.com/watch?v=GuQxLqAc5GE> 这个好像连pcb也不需要
* <https://github.com/LeafCutterLabs/bigKNOB> 这个很简洁，但是没有开放pcb
* <https://www.youtube.com/watch?v=IDlcxLQ1SbY> base on Arduino Pro Micro, so PCB is simple. But it not open source neither.
* [Adafruit MacroPad](https://www.adafruit.com/product/5128) 通过旋钮更换布局，有个小屏幕显示当前布局。挺有创意。这个旋钮只能更换布局么？我希望它还能用来控制音量大小。Adafruit 也做了很多其他的小键盘。

### 分裂键盘
* <https://keeb.io/collections/split-keyboards> 感觉这种不要外壳的也可以接受，不过淘宝上居然很少。
* <https://github.com/foostan/crkbd> Corne & <https://github.com/joric/jorne> Jorne，带小屏幕，wiki很详细

### 按键延时
测试网站 <https://en.key-test.ru/>

从算法上，并不是每个键都连了一个GPIO，而是每排和每列连接一个GPIO，键盘每排的按键是并连的，也就是说任何一个接通都会设置这个GPIO为接通，然后算法先遍历每排，然后遍历每列。这种叫矩阵扫描。

机械键盘矩阵扫描并不是延时的最大问题，而是去抖比较耗时。

如何全键无冲呢？感觉FPGA可以在一个指令里面检查所有GPIO状态吧，其他的客制化键盘都支持么？avr 的也不支持吧，还是遍历速度够快就可以了么？

延迟和回报率这两者是一回事么？

[【外设科普1】键盘、机械键盘按键延迟原理](https://www.bilibili.com/video/BV1Cs411V756?spm_id_from=333.999.0.0) B站视频，讲的还比较全面。

"RealKey Technology是CHERRY于2015 年CES发布的技术，搭载于当时的 CHERRY MX-BOARD 6.0键盘上，后来因为一些原因导致RK技术不得不更换成现在的HS（High Speed）技术，但其可以做到键盘触发1毫秒响应以及真正意义上的全键盘无冲等得以保留。而且HS技术取代一般机械键盘的信号交叉触发技术，以一个控制器为单位，令每一个按键变成一个独立触发点，并连接高度敏感的模拟。让按键触发不再需要横竖信号交叉触发，可以直接单点触发并及时反馈，可以做到真正的无延时。"

### 社区
* CMMK <https://www.chiphell.com/forum-312-1.html>
* zFrontier装备前线 <https://www.zfrontier.com/>

### Think
* 客制化键盘的市场超出我的想象，各种开车组团，大大小小商家不计其数
* 更多买家似乎更关心轴体外壳这种硬件，我更关心能不能刷机。很多定制键盘价格已经破千，这对于我来说已经超出了极客/垃圾佬的精神
* keymap、键帽和轴体，这些特别能体现个人喜好，而做出选择却很容易：keymap 直接图形化修改，即使生效；轴体可以热拔插；价格范围也很大。而这些眼花缭乱的选择却不影响键盘基本功能的稳定性。技术上不管是开源还是键盘出厂固件都能很容易的处理键盘的输入要求。
