---
title: 为BeagleBone配个显示器吧
author: Fan Fan
layout: post
permalink: /2012/04/30/%e4%b8%babeaglebone%e9%85%8d%e4%b8%aa%e6%98%be%e7%a4%ba%e5%99%a8%e5%90%a7/
dsq_thread_id:
  - 670180379
categories:
  - 电子制作
---
原装的LCD CAPE要900￥，日，抢钱啊，taobao上搜“tft 液晶屏”几十块的一大堆。<del>可这东西没有显示器不能显示其强大啊</del>（按照其文档说所，这个可以用Node.js来展现），自己折腾个吧。

1. [Sharp的货][1]，4.3&#8243;，最早在Sparkfun上发现，PSP用，Sharp的货啊，ISP硬屏么？不管怎么样听上去就NB轰轰。taobao卖130￥。接口40Pin，8位RGB数据线：R0～R7，G0～G7，B0～B7。不知道这是何种接口（RGB是24位真彩色）。这种大的LCD可能没有带drive芯片。可以直接和ARM连结。友善之臂的[tiny6410][2]就是这么做的，我没看到中间有驱动芯片。这种工业级板子都只有扁平线，要自己弄的话，还得买个FPC连接器，然后taobao上面有吧FPC转DIP的座子卖，不过看上面的Sparkfun的示意图还有电阻电容，看来有点麻烦。这里有个教程[Series 1 &#8211; Display Driver Boards][3]，说的就是如何驱动这个Sharp屏，事无巨细，着实佩服！

2. 带驱动芯片的小屏。比如taobao上的[这个][4]和[这个][5]。LCD控制芯片分别为SSD1289和ILI9325，数据线分别为16根和18(3*6=18)根，奇怪。但是都提供转接板，这样容易焊接也容易使用，价格也很便宜。可能这种模式的TFT是给AVR一般的MCU用的。[这个][6]是7寸屏幕带触摸的转接板。感觉如果弄个跟手机差不多的屏幕就没多大意思了。

3. 我自己有个Zune mp3 player，由于不能显示中文。这货屏幕还算细腻，拆下来看了下，23针接口，双排的，间距0.3mm，要个FH26-23S-0.3SHW[连接器][7]。也不知道这货是否带控制芯片，点亮估计有点难。

这些屏幕如果使用ARM+Linux做系统，哪种更方便呢？是否需要驱动？这个我还没有看到例子。

4. 秉着先难后易的原则，先搞个简单的点吧。有个[beaglebone+ssd1289][8]的成功案例，就先照猫画虎。周末折腾了下这个，这个案例用的是openEmbedded，配置make kernel的过程时间漫长，<del>而且在ubuntu编译上有很多错误</del>（删掉重来就可以了），看来这东西还是太庞大。

考虑到Ubuntu ARM对无线支持更好，而且可以用apt-get，于是想转为用Ubuntu的[kernel][9]。这个kernel是基于标准的Linux kernel，然后再上面打patch，感觉流程更简单。于是目标变为把对openEmbedded的[patch][10]转为对Linux Kernel的patch，可惜文件不能一一对应。其中最主要的是/arch/arm/mach-omap2/board-am335xevm.c这个文件在标准kernle里面没有。Angstrom版本所有的kernel patch在[这里][11]，我想它应该也是对标准的kernel进行patch，但是我不知道它是如何产生**board-am335xevm.c**这个文件的。一根筋上来了，在github上找了个遍也没有发现，后来在TI的[AM335xSDK][12]里面的PSP Source Code发现了这个文件，里面有完整的Linux Kernel，看来这个C文件不是通过patch产生的。难道openEmbedded会把这个下载到本地？TI没有放到那个git源中么或者push到主的kernel？

[<img class="alignnone size-large wp-image-1199" title="IMG_2580" src="http://fkpwolf.net/WordPress/wp-content/uploads/2012/04/IMG_2580-1024x768.jpg" alt="" width="580" height="435" />][13]

&nbsp;

上面是显示的样子：3.2寸小屏、SSD1289驱动芯片。但是有个问题：屏幕不稳定，要RESET beaglebone两三次之后才会正常显示，可能是驱动的兼容性问题。暂时没有办法解决。后来听说LCD的线对显示也很重要，于是费老大劲做了排线，尽量把线做短些。再试，问题依旧。

放弃吧，还是用raster  mode吧。

如果build kernle的过程种，openEmbedded没有弹出menuconfig的console， 安装[libncurses5-dev][14]这个包即可。

 [1]: http://www.sparkfun.com/products/8335
 [2]: http://www.arm9.net/tiny6410.asp
 [3]: http://www.recontech.co.uk/index.php/projects/series-1-a-lq043t3dx02-display-driver-board
 [4]: http://detail.tmall.com/item.htm?id=13072160020&
 [5]: http://item.taobao.com/item.htm?id=13949148896
 [6]: http://item.taobao.com/item.htm?id=9939584126&
 [7]: http://item.taobao.com/item.htm?id=7846764267
 [8]: http://www.cemetech.net/forum/viewtopic.php?t=7814
 [9]: https://github.com/RobertCNelson/stable-kernel
 [10]: http://www.cemetech.net/programs/index.php?mode=file&id=749
 [11]: https://github.com/Angstrom-distribution/meta-ti/tree/master/recipes-kernel/linux
 [12]: http://software-dl.ti.com/dsps/dsps_public_sw/am_bu/sdk/AM335xSDK/latest/index_FDS.html
 [13]: http://fkpwolf.net/WordPress/wp-content/uploads/2012/04/IMG_2580.jpg
 [14]: http://lists.linuxtogo.org/pipermail/angstrom-distro-devel/2011-August/004911.html