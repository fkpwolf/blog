---
layout: post
title: "键盘"
date: 2021-8-8
categories:
  - DIY
typora-root-url: ../../../blog
---

### 键帽
* Cherry G80-3000 白色键盘的键帽是pbt，黑色的键盘帽是pom的
* 关于键帽表面材质以及印刷工艺的详细介绍 <https://www.wstx.com/p-20810-1>
* Filco 圣手87，虽然是ABS键帽，但是比PBT的那种磨砂感更为细腻。不过这在这种很少了。ABS 缺点是更容易打油，但是更容易做出不同色彩和配色的键帽。

### 轴体
* TTC 快银轴，无段落，比较热门的轴体。
* 佳达隆 G 黄轴，无段落，手感柔弱，但压到底后回弹有力，性价比高。
* Box Navy 段落轴，挺硬的
* Romer-G，罗技和欧姆龙合作出品，双触点，轴柱特殊导致很难换键帽

### PCB
多模支持多种模式，比如有线、2.4G无线和蓝牙无线。大多数是一个单片机加上蓝牙模块来支持多模，这对蓝牙模块 MCU 其实是浪费。
对于热拔插键盘，PCB会包含热拔插轴座，否则需要手工直接焊接轴体到PCB上面。
不管PCB是否支持热拔插，用的轴体是通用的，只是要注意轴体有3针和5针两种规格。

### 组装
除了上面基本部件，还需定位板和外壳。
定位板是用来防止轴体晃动的部件。
外壳则是包含上面所有部件的外壳。常见的有GH60通用外壳，个人比较喜欢黑透款式，塑料材质，这样整块键盘可以轻松拿起，有樱桃原厂无钢板键盘的风格。

### 配列
所谓配列，就是键盘布局。包含数字小键盘的全尺寸的是104布局，这个最常见。去掉数字键盘但是包含F1-F12功能键的是87布局，这个算是成品键盘里面最常见了，其中IKBC是个不错的选择。
去掉F1-F12功能键和方向键就是60布局了，这个在客制化键盘里面最常见。去掉F1-F12功能键一般人还能适应，因为用的不多。但是去掉了方向键而改用组合键来实现上下左右，这个挺多人不适应。
所以就有了加上方向键的64布局。

### 开源键盘
* SMK68: First Community-Driven OpenSource RISC-V Custom Mechanical Keyboard~ with BL706. With Programmable OLED Keys, RGB Led, Hot-swappable, USB/BLE Dual-Mode, vibration feedback, and even Serial Terminal ! <https://github.com/sipeed/sipeed_keyboard>
* <https://github.com/Lotlab/Lot60-BLE-Keyboard>  全开源，<https://wiki.lotlab.org/index.html>
* [Makerdiary M60](https://makerdiary.com/pages/m60-mechanical-keyboard)，我买的第一个热拔插键盘
* System76 Launch Configurable Keyboard <https://github.com/system76/launch>

### 软件
* QMK <https://qmk.fm/> 大多数有线键盘都基于这个方案
* VIA <https://caniusevia.com/> 修改键盘配列的一个图形界面软件。这个软件强的地方在于修改后立即生效，没有保存的动作。而且下次修改的时候会加载键盘里面的配置，所以是无缝连接。如果能让一款成品键盘，比如IKBC的支持VIA，那是极好的。不过为什么要改标准布局的键盘的布局呢？

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

### 按键延时
从算法上，并不是每个键都连了一个GPIO，而是每排和每列连接一个GPIO，键盘每排的按键是并连的，也就是说任何一个接通都会设置这个GPIO为接通，然后算法先遍历每排，然后遍历每列。这种叫矩阵扫描。

机械键盘矩阵扫描并不是延时的最大问题，而是去抖比较耗时。

如何全键无冲呢？感觉FPGA可以在一个指令里面检查所有GPIO状态吧，其他的客制化键盘都支持么？avr 的也不支持吧，还是遍历速度够快就可以了么？

延迟和回报率这两者是一回事么？

[【外设科普1】键盘、机械键盘按键延迟原理](https://www.bilibili.com/video/BV1Cs411V756?spm_id_from=333.999.0.0) B站视频，讲的还比较全面。

"RealKey Technology是CHERRY于2015 年CES发布的技术，搭载于当时的 CHERRY MX-BOARD 6.0键盘上，后来因为一些原因导致RK技术不得不更换成现在的HS（High Speed）技术，但其可以做到键盘触发1毫秒响应以及真正意义上的全键盘无冲等得以保留。而且HS技术取代一般机械键盘的信号交叉触发技术，以一个控制器为单位，令每一个按键变成一个独立触发点，并连接高度敏感的模拟。让按键触发不再需要横竖信号交叉触发，可以直接单点触发并及时反馈，可以做到真正的无延时。"

### 社区
* CMMK <https://www.chiphell.com/forum-312-1.html>