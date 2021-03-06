---
title: 给小度路由更换128M内存（BGA）
author: Fan Fan
layout: post
permalink: /2014/02/28/change-128m-bga-memory-for-xiaodu-router/
aktt_notify_twitter:
  - no
categories:
  - 电子制作
---
[<img class="alignnone size-medium wp-image-1494" title="IMG_20140223_112314" src="http://fkpwolf.net/WordPress/wp-content/uploads/2014/02/IMG_20140223_112314-e1393571259360-300x169.jpg" alt="" width="300" height="169" />][1]

## 1. 配置

CPU: MT7620A  
内存: 64MB DDR2 Winbond  
FLASH: 8MB 内存 型号为 MX25L6405D

## 2. 内存

128M DDR: SAMSUNG K4T1G164QQ-HCF7。这个是我刷机用的型号。 256M的三星的没有，只有美光MT47H128M16或者现代H5PS2G63EMR。不过256M太贵，搞坏一个不得伤心死？只好作罢。

## 3. 取下来

由于内存芯片是BGA封装，只能使用热风枪。先涂好助焊膏，芯片表面和四周多涂点。开始加热，温度我设置成400度。先把芯片周围预热，这样避免直接加热芯片时升温过快。然后正对芯片吹，上下晃动，不要固定在一个点。这个时候你会看到助焊膏融化的很快。

要让芯片完全“**浸润**”，看上去是在**油里面煎饼**一样。这样热量才能迅速传到BGA下面的焊点。

大约5分钟后，用镊子压下芯片，如果芯片能动，说明可以取下来，否则继续加热。反正这个芯片不准备要了，不要怕。千万不要强行取下，这样很容易扯下电路板上的焊盘，那就欲哭无泪了。“焊者”就亲自搞坏一个小度路由，还好只有99￥。

取下后，用电烙铁整理下焊盘。然后用洗板水洗下板子。

## 4. 植锡球

买来的内存芯片已经植好焊球，但是一般都不容易焊接，可能都是无铅的吧。手工自己植不难，要点耐心而已。买好对应BGA钢网，值珠台，锡球。都很便宜的。

锡球放上去后，先加热会，然后用助焊膏轻轻的在锡球上面抹一层，因为芯片这个时候已经有点温度，助焊膏很容易融化。继续加热。如果不加助焊膏的话，容易虚焊，当分开钢网和芯片时，这些虚焊的锡球就会脱落。分离时先放到洗板水里泡会。

## 5.焊上去

这个就是最难的一步了，没成功的话得反复来几次。

先在电路板上的焊盘上涂点助焊膏，抹均匀。然后放上芯片，在芯片上面涂助焊膏。这时和取下来的时候比较像，助焊膏要“浸润”芯片，主要是芯片的四个边。加热后，如果发现助焊膏不够，用助焊膏在周围轻轻的抹一下。

加热大概4分钟，用镊子轻轻的压下芯片，如果芯片能压下去然后反弹起来，说明焊接好了，这个时候锡球有一定张力。不然继续加热。加热后最好不要再挪动芯片位置，如果有细微偏差，锡球和焊盘粘在一起时会把芯片归为的。

从上面可以看到，BGA芯片很大程度上依赖助焊膏的操作。

[<img class="alignnone size-full wp-image-1503" title="Untitled" src="http://fkpwolf.net/WordPress/wp-content/uploads/2014/02/Untitled.png" alt="" width="513" height="401" />][2]

不要害怕温度过高。我一块内存芯片“拆-焊-拆”反复好几次也没有问题。芯片周围如果有大的电容，最好先取下来，焊接好芯片后再放回去。

 [1]: http://fkpwolf.net/WordPress/wp-content/uploads/2014/02/IMG_20140223_112314.jpg
 [2]: http://fkpwolf.net/WordPress/wp-content/uploads/2014/02/Untitled.png