---
title: 在酷比魔方（Cube）U30GT上面安装Ubuntu
author: Fan Fan
layout: post
permalink: /2013/09/03/%e5%9c%a8%e9%85%b7%e6%af%94%e9%ad%94%e6%96%b9%ef%bc%88cube%ef%bc%89u30gt%e4%b8%8a%e9%9d%a2%e5%ae%89%e8%a3%85ubuntu/
aktt_notify_twitter:
  - no
categories:
  - 电子制作
---
[<img class="alignnone size-medium wp-image-1452" title="IMG_20130830_134340" src="http://fkpwolf.net/WordPress/wp-content/uploads/2013/09/IMG_20130830_134340-225x300.jpg" alt="" width="225" height="300" />][1]

这台机器用的是双核RK3066，1G内存，好处在于有现成的Kernel代码。四核的RK3188的我折腾过Pipo S1 pro，LCD死活出不来。

## 编译Kernel

代码从这里（https://github.com/Galland/rk3x\_kernel\_3.0.36）checkout，config文件和board文件从这里（[https://github.com/crewrktablets/rk3x\_kernel\_3.0.36][2]）拿，有了这两个文件就省事多了。原来的config文件是为Android系统准备的，需要做些小的修改：

1）CONFIG\_CMDLINE\_FORCE=y 这样就能读取定制的command line参数；

2）CONFIG\_INITRAMFS\_SOURCE=&#8221;../initramfs/initramfs.cpio。 接下来就和一般的RK3066无区别了；

3）关掉CONFIG\_ANDROID\_PARANOID_NETWORK，不然只有root才能访问网络。

##  刷机

这里要确定机器的recovery位置，这个值可从机器的stock rom里面取得。

## WIFI

这个比较容易，RK903现在大家了解比较多了。可以直接使用bcm40181来驱动，注意firmware要从stock rom里面拿，具体的版本可以从Android系统的dmesg里面找到。然后把这个驱动放到/etc/modules里面来手工启动。

## 未完成

其他的统统不能work，比如触摸屏，摄像头。。。

 [1]: http://fkpwolf.net/WordPress/wp-content/uploads/2013/09/IMG_20130830_134340.jpg
 [2]: https://github.com/crewrktablets/rk3x_kernel_3.0.36