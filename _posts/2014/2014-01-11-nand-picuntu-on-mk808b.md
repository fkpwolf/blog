---
title: NAND Picuntu on MK808B
author: Fan Fan
layout: post
permalink: /2014/01/11/nand-picuntu-on-mk808b/
aktt_notify_twitter:
  - no
categories:
  - 电子制作
---
因为这个设备所有信息都存在NAND上面，所以有刷成砖的可能。下面采用改一小步验证一小步的方法，保证每步正确。注意，这样也**不能保证**不会出现机毁人亡的悲剧。

1. 一个可以从SD卡启动的picuntu。

2. 修改可以工作的picuntu的kernel image CMDLINE。也就是类似mtdparts=rk29xxnand:0x00002000@0x00002000(misc)这样的定义。这个是类是硬盘分区表的东西，但是NAND比较简单，本身不存这些信息。

如果是SD卡启动，这个参数是没有用的，因为你没有使用到内置的NAND。也就是说，即便是错误的，机器仍然可以启动<span style="font-size: 13px;">。所以这个要修改成你自己机器的实际定义。所以这步是很重要的。这里也可以修改layout，这样有更多NAND空间。</span>

3. 修改initrd文件，加上rk30xxnand.ko，这个是读写nand的驱动，他会读取上面的mtdparts的参数。注意版本。

解压： cpio -id < cpiofile

修改后重新压缩：cpio &#8211;create &#8211;**format**=&#8217;newc&#8217; >../newinitrd

4.重现编译后先把recovery.img刷到recovery分区，确认initrd文件能用，确认新的NAND驱动能工作。然后把recovery.img刷到kernle分区。也有可能是boot分区。按照引导流程来说应该是先到boot分区找kernel，然后再到kernel分区找。

5. 重启。这个时候kernel已经在NAND上面，但是rootfs还是用的SD卡。首先确认/proc/mtd里面信息正确。然后mount。然后从SD卡拷贝文件系统到NAND。拷贝后sync下。

6. 最后修改CMDLINE root=/dev/mtdblock3，这个是你在/proc/mtd看到的分区。打包后重新刷recovery.img。