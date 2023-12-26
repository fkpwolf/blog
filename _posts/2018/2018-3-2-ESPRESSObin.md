---
layout: post
title:  "ESPRESSObin"
date:   2018-3-2 14:20:33
typora-root-url: ../../../blog
---

### Intro
![Image](/images/2018/ESPRESSObin-arch.png)
ESPRESSObin [wiki](http://wiki.espressobin.net/tiki-index.php?page=About+ESPRESSObin)，两路SRGMII, 1路RGMII。  

从上面看，5V的micro usb也可以启动板子，实际使用接usb和接dc都会亮灯，只接USB时 PC 能看到新的串口设备，但是只有接12v时候tty才会有输出。是 5V 电流不够么？

Run `cat /proc/cpuinfo` and get:

    Features        : fp asimd evtstrm aes pmull sha1 sha2 crc32 cpuid

There is no FPU(fp?). "The aarch64 target does not support a --with-fpu configure option (or an -mfpu command-line option) because an FPU is assumed to be present by default." Really?

### u-boot
现在可以从spi启动，然后呢？能从emmc启动么？
参考这里 <http://wiki.espressobin.net/tiki-index.php?page=Boot+from+removable+storage+-+OpenWrt>
```
setenv bootmmc 'mmc dev 1; ext4load mmc 1:1 $kernel_addr $image_name;ext4load mmc 1:1 $fdt_addr $fdt_name;setenv bootargs $console root=/dev/mmcblk0p1 rw rootwait; booti $kernel_addr - $fdt_addr'
saveenv
run bootmmc
```
默认启动要bootcmd，我直接把上面的值付给了它。
https://www.armbian.com/espressobin/ 这里可以升级u-boot，我用 flash-image-1g-2cs-800_800.bin(DDR3-MEM-RAM_CHIPS-CPU_DDR)，直接从 usb 里面读取文件刷机。重启后 CPU 800 MHz，我记得刷之前是1G MHz。Boot priority with this script is: 1. USB, 2. SATA, 3. SD card, 4. eMMC 但是看不到也没法启动我的eMMC，使用U盘烧录armbian，可以启动。mmc 0 是 sd card。刷机后有时候会使env混乱，需要重新设置env。
现在问题的uboot无法检测到emmc，linux可以。
https://github.com/armbian/build/issues/851 armbian support emmc patch
我提了个问题 https://forum.armbian.com/topic/9670-espressobin-u-boot-cant-detect-emmc/
https://github.com/armbian/build/blob/master/config/sources/mvebu64.conf 定义编译代码源，u-boot 不可以，说明其 dts 不对，linux 可以，说明其 dts 是对的。https://github.com/MarvellEmbeddedProcessors/u-boot-marvell/blob/u-boot-2018.03-armada-18.12/arch/arm/dts/armada-3720-espressobin.dts 这个里面我看已经加上 sdhci1 patch 了，但是status = "disabled"，这说明 armbian 的 patch 没有加上（因为代码不匹配）？
ok，我自己来[编译](http://wiki.espressobin.net/tiki-index.php?page=Build+From+Source+-+Bootloader) u-boot 吧。Your GCC is older than 6.0 and is not supported. Centos 上面需要 7.4 gcc，没办法，还得自己[编译](https://bdznh.github.io/2018/04/16/install-gcc-7-3-on-centos-7-4/)。花了半天时间编译成功，后来发现还是一样问题 linaro gcc 换成更新版本7.4.1就可以了。make[2]: “arch/arm/dts/armada-3720-espressobin.dtb”是最新的。我修改了 arch/arm/dts/armada-3720-espressobin.dts，应该要重新生成 dtb 才对啊，查看时间戳，确实是最新的。
make: *** DDR_TYPE=DDR3: 没有那个文件或目录。 停止。 https://github.com/ARM-software/tf-issues/issues/665
make DEBUG=1 USE_COHERENT_MEM=0 LOG_LEVEL=20 SECURE=0 CLOCKSPRESET=CPU_1000_DDR_800 DDR_TOPOLOGY=2 BOOTDEV=SPINOR PARTNUM=0 WTP=/home/fan/A3700-utils-marvell/ MV_DDR_PATH=/home/fan/mv-ddr-marvell PLAT=a3700 all fip
main.c:40:20: 致命错误：string.h：没有那个文件或目录 https://bugzilla.redhat.com/show_bug.cgi?id=1424592 得，最后一步换成 ubuntu，成功。刷机后可以在 u-boot 中看到 emmc。
如何拷贝 image 到 emmc 呢? 我直接使用 dd if=xxx.img of=/dev/mmcblk1 bs=10M，没有成功。

    command: `ls mmc 1:1 / ` can list file under u-boot.
    Marvell>> ext4load mmc 1:1 ${scriptaddr} /boot/boot.scr
    1119 bytes read in 10 ms (108.4 KiB/s)
    Marvell>> source ${scriptaddr}
    ## Executing script at 06d00000
    ** Bad device :1 0x6d00000 **
    ** Bad device 0:1 0x7000000 **
    ** Bad device 0:1 0x1100000 **
    ** Bad device 0:1 0x6000000 **
    ** Bad device 0:1 0x6000000 **
    Bad Linux ARM64 Image magic!

原来 boot.src 是错的了。boot.cmd 中：
    ext4load $boot_interface 0:1 $kernel_addr ${prefix}$image_name
应该是 1:1，我修改 boot.cm 后，按照文件末尾指示（不错）运行 mkimage 重新生成 boot.scr，然后就可以了。
似乎 armbian u-boot 只是多了这个 boot.scr 可以从多个地方启动，其他并没有改进。
一天之后就 kernel panic，什么鬼，Internal error: synchronous parity or ECC error 提了个 [issue](https://forum.armbian.com/topic/9778-espressobin-hang-after-one-day/)。same issue [here](https://forum.armbian.com/topic/9778-espressobin-hang-after-one-day/). Looks cpu scale not working. `cpufreq-set -g performance` try this at first. Now stable. To permanently save changes, write /etc/default/cpufrequtils & /etc/init.d/cpufrequtils file. 运行一段时间发热严重，而且也会掉 ssd 和死机，按照[这里](https://forum.armbian.com/topic/4089-espressobin-support-development-efforts/page/27/?tab=comments#comment-79518)升级 u-boot，WTMI is updated to 18.12.1，这次用了flash-image-ddr3-1g-2cs-800_800.bin，以前的是1000mz，不知道是否和这个有关系。

### OpenWrt 

I download file `openwrt-19.07.3-mvebu-cortexa53-globalscale_espressobin-ext4-sdcard.img.gz` from <https://downloads.openwrt.org/releases/19.07.3/targets/mvebu/cortexa53/> and flash it into SD card firstly.
if boot into eMMC, run `run bootcmd_mmc0` in uboot to boot from sd card. But soon I found in OpenWrt I can't find eMMC disk, only sd card.
I realise that I need openwrt-19.07.3-mvebu-cortexa53-globalscale_espressobin-emmc-ext4-sdcard.img.gz. This type of Openwrt can detect eMMC. So the later image can be only used on the eMMC board? Otherwise it is not necessary to create two different images.
Now I am in SD card OpenWrt, flash image by `dd if=xxx.img of=/dev/mmcblk1 bs=10M`. Reboot and get error:

    [    4.580113] VFS: Cannot open root device "PARTUUID=15896539-02" or unknown-block(179,26): error -117
    [    4.589621] Please append a correct "root=" boot option; here are the available partitions:
    [    4.598413] 1f00            4096 mtdblock0
    [    4.598419]  (driver?)
    [    4.605232] b300         3866624 mmcblk0
    [    4.605238]  driver: mmcblk
    [    4.612138]   b301         3657728 mmcblk0p1 04b226a1-01
    [    4.612143]
    [    4.619196] b310            2048 mmcblk0boot1
    [    4.619200]  (driver?)
    [    4.626433] b308            2048 mmcblk0boot0
    [    4.626437]  (driver?)
    [    4.633531] b318        15558144 mmcblk1
    [    4.633536]  driver: mmcblk
    [    4.640445]   b319           16640 mmcblk1p1 15896539-01
    [    4.640449]
    [    4.647487]   b31a          262400 mmcblk1p2 15896539-02

oh, I should run `dd if=xxx.img of=/dev/mmcblk0 bs=10M`. In OpenWrt, the device is eMMC. Then boot OK.

eMMC resize
eMMC can't be detected under Armbian. And OpenWrt(eMMC version) didn't have appropriate tool like `diskpat`.

### PCIe
To enable my mini PCIE-e Atheros AR928X Wireless, I need install following package:
* ath9k-htc-firmware
* kmod-ath9k
* kmod-ath9k-common
* wpad-mini

sata 接了个 SSD，感觉速度还可以：

    fan@espressobin:~$ sudo hdparm -tT /dev/sda1
    /dev/sda1:
    Timing cached reads:   1174 MB in  2.00 seconds = 587.01 MB/sec
    Timing buffered disk reads: 778 MB in  3.00 seconds = 259.28 MB/sec

samba 拷贝能到 80 MB/s，没有仔细测试。

![Image](/images/2018/ESPRESSObin-run.jpg)
上图是当前的使用状态，接了个mini PCIE网卡，一块笔记本硬盘。对比BPI-R2或者BPI-R64，优点在于已经有很
稳定的、官方的OpenWrt版本可以使用，这点很重要，因为我不想每次安装一个新的包的时候都需要自己编译。
