---
layout: post
title:  "带远程管理的服务器"
date:   2016-8-11 14:20:33
---

E5 2670 v1, 680￥，8核16线程。好像c2的才支持VT-D，不可能吧。
可以用i7 3960x, 要x79的主板 ，X79-E WS 或者X79 WS均为不错的选择
http://www.chiphell.com/thread-1450973-1-1.html  作死的节奏: 双路e5 2670攒机直播
http://www.chiphell.com/thread-1438514-1-1.html  一直向往的2011双路，终于得偿所愿，技嘉GA-7PESH4+E5-2670 X 2

主板用超微 SUPER X9DRL-if，或者taboo "2011 双路 主板”，记得是V1的CPU。
超微 X9DRI-F，兼容V1和V2。是这样的么？1900就能买全新3年保修的Z9PA-D8C了，但是他的手册里面没有提到SR-IOV。太老了么？8条4G的ECC REG 1600的内存，一共大约360

不过上面这个平台主板不好找，或者换新点的x99主板，配合"X宝的E5 2683V3为毛很便宜？能入么？" http://www.chiphell.com/thread-1447908-1-1.html 14核28线程, 2500￥。x99主板美国亚马逊便宜很多。
E5 2675V3 是16核32线程, 但是主频太低了, 才1.8G主频,16核满载2.0G, 单核最大睿频2.3G
x99的i7 cpu一般选5820k, 6核。e5 v3 cpu里面便宜的还有E5-2603 V3，6核，880￥。不过E5 2683V3好像卖的人比较多。

Z170 的U是14NM X99是22NM X99 为6-8核U Z170 U最大4核 X99 为4通道内存 Z170为 2通道内存 X99没有核显 Z170有核显 X99为钎焊 Z170为硅脂。

https://www.asus.com/Commercial-Servers-Workstations/Z10PAD8/HelpDesk_Manual/
CPU2没有暴露PCI-E接口？这么浪费？CPU1和CPU2是怎么通信的？
QPI是一种点对点互联结构 https://zh.wikipedia.org/wiki/%E5%BF%AB%E9%80%9F%E9%80%9A%E9%81%93%E4%BA%92%E8%81%94
Ref http://www.doit.com.cn/article/2013-03-25/3408752.shtml 就拿双英特尔至强E5 LGA2011平台为例，PCIe扩展槽直接连接到一个CPU。
超微X10DRL-i, 注意E-ATX主板

[<img src="images/2016/1AA68EF2-EE94-41AD-8717-A70B69F6934D.png"/>]
