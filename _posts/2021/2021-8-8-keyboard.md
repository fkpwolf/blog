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

### 按键延时
从算法上，并不是每个键都连了一个GPIO，而是每排和每列连接一个GPIO，键盘每排的按键是并连的，也就是说任何一个接通都会设置这个GPIO为接通，然后算法先遍历每排，然后遍历每列。这种叫矩阵扫描。

如何全键无冲呢？感觉FPGA可以在一个指令里面检查所有GPIO状态吧，其他的客制化键盘都支持么？avr 的也不支持吧，还是遍历速度够快就可以了么？

延迟和回报率这两者是一回事么？

[【外设科普1】键盘、机械键盘按键延迟原理](https://www.bilibili.com/video/BV1Cs411V756?spm_id_from=333.999.0.0) B站视频，讲的还比较全面。

"RealKey Technology是CHERRY于2015 年CES发布的技术，搭载于当时的 CHERRY MX-BOARD 6.0键盘上，后来因为一些原因导致RK技术不得不更换成现在的HS（High Speed）技术，但其可以做到键盘触发1毫秒响应以及真正意义上的全键盘无冲等得以保留。而且HS技术取代一般机械键盘的信号交叉触发技术，以一个控制器为单位，令每一个按键变成一个独立触发点，并连接高度敏感的模拟。让按键触发不再需要横竖信号交叉触发，可以直接单点触发并及时反馈，可以做到真正的无延时。"
机械键盘矩阵扫描并不是延时的最大问题，而是去抖比较耗时。

### 开源键盘
多模只支持多种模式，比如有线、2.4G无线和蓝牙无线。大多数是一个单片机加上蓝牙模块来支持多模，这对蓝牙模块 MCU 其实是浪费。
* SMK68: First Community-Driven OpenSource RISC-V Custom Mechanical Keyboard~ with BL706. With Programmable OLED Keys, RGB Led, Hot-swappable, USB/BLE Dual-Mode, vibration feedback, and even Serial Terminal ! <https://github.com/sipeed/sipeed_keyboard>
* <https://github.com/Lotlab/Lot60-BLE-Keyboard>  全开源，<https://wiki.lotlab.org/index.html>
* [Makerdiary M60](https://makerdiary.com/pages/m60-mechanical-keyboard)

### 软件
* QMK <https://qmk.fm/> 大多数有线键盘都基于这个方案
* VIA <https://caniusevia.com/> 修改键盘配列的一个图形界面软件。这个软件强的地方在于修改后立即生效，没有保存的动作。而且下次修改的时候会加载键盘里面的配置，所以是无缝连接。如果能让一款成品键盘，比如IKBC的支持VIA，那是极好的。不过为什么要改标准布局的键盘的布局呢？

### DIY无线优联机械键盘
这种方式是拿罗技的主控板，加上定制PCB来实现键盘。因为罗技的无线技术还是很牛的：省电、稳定，这两点对于无线键盘来说都很重要。
