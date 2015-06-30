---
title: Android上的水波纹（water ripple）效果
author: Fan Fan
layout: post
permalink: /2009/10/08/android%e4%b8%8a%e7%9a%84%e6%b0%b4%e6%b3%a2%e7%ba%b9%ef%bc%88water-ripple%ef%bc%89%e6%95%88%e6%9e%9c/
dsq_thread_id:
  - 605709462
categories:
  - Mobile
---
最近想看看Android上面的图像性能，于是做了个例子来测试一下：动态的2D水波纹。

1）最先找到的例子是在这里 [WaterFilter][1]， 这个网站上也有很多其他的图像处理滤镜。代码是基于awt的，转换到android 2d也不是很困难。由于其用到了很多三角函数，比如sin/cos，最后动态运行速度非常慢，但是效果应该是最逼真的。

2）[Java Water Simulation][2]对算法进行了优化，把三角函数转化成普通的加减乘除，也是一种近似的算法，具体的原理在[这里][3]，如果对比前后的效果，还是有化腐朽为神奇的感觉的。

<img class="alignnone size-full wp-image-425" title="device" src="http://fkpwolf.net/WordPress/wp-content/uploads/2009/10/device.png" alt="device" width="320" height="480" />

原来学校里学的图像处理已经忘的差不多了，上次看到科学松鼠会的一篇文章[ “玩伴女郎”误入学术圈 ][4]才记起原来学过这门课，惭愧啊。

图像图形算法一般计算都比较密集，如果要加快速度，在保证一定的质量时，“奇巧淫技******”还是很多的。代码相对于AWT版本有些地方还是效率差。AWT中有个类MemoryImageSource，这个类可以在图像处理中，直接对内存中的图像进行“就地”的操作，在android中没有对应的类，处理图像数据有一个“源”到“目的”的转换，速度可能就有损耗。Android的Canvas有一个drawBitmapMesh的方法，对图像进行矩阵操作，可能会加快速度，暂时还对其了解不多。

比较AWT，android的2D图像库还是很单薄的，稀稀拉拉的就几个类。核心的方法都是native的，也就是说是通过jni调用的。

源代码在这里：http://code.google.com/p/android-workspace/

 [1]: http://www.jhlabs.com/ip/filters/WaterFilter.html
 [2]: http://www.neilwallis.com/java/water.html
 [3]: http://freespace.virgin.net/hugo.elias/graphics/x_water.htm
 [4]: http://songshuhui.net/archives/16022.html