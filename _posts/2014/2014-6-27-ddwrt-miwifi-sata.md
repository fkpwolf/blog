---
layout: post
title:  "DD-WRT MiWiFi SATA"
date:   2014-6-27 14:20:33
typora-root-url: ../../../blog
---

ASM1062 : PCIe to x 2 SATA 6G and PATA Controller  

linux-3.10/drivers/ata/ahci.c has supported it. 

CONFIG_ATA=y CONFIG_SATA_AHCI(? <http://cateee.net/lkddb/web-lkddb/SATA_AHCI.html> ) 

 CC      drivers/ata/libata-transport.o 

In file included from drivers/ata/libata-eh.c:47:0: 
```
include/linux/libata.h:518:2: error: expected identifier before numeric constant 
  OFF, 
  ^ 
```
<http://stackoverflow.com/questions/6289376/im-getting-an-error-concerning-enum-i-think> the OFF has been difined at other place. ==>how to fix? 
```
enum sw_activity { 
​        OFFF, /**OFF was defined other packe FIXME fan**/ 
​        BLINK_ON, 
​        BLINK_OFF, 
}; 
```
/home/fan/dd-wrt-mirror/src/linux/universal/linux-3.10/brcm/arm/include/typedefs.h:367:13: error: expected identifier before numeric constant 

\#define OFF 0 

4.4 kernel没有上面的编译问题，但是启动后： 
```
ata1: SATA max UDMA/133 abar m512@0x48000000 port 0x48000100 irq 175 
ata2: SATA max UDMA/133 abar m512@0x48000000 port 0x48000180 irq 175 
ata1: SATA link up 1.5 Gbps (SStatus 113 SControl 300) 
ata1.00: failed to IDENTIFY (I/O error, err_mask=0x100) 
ata1: SATA link up 1.5 Gbps (SStatus 113 SControl 300) 
ata1.00: failed to IDENTIFY (I/O error, err_mask=0x100) 
ata1: limiting SATA link speed to 1.5 Gbps 
ata1: SATA link up 1.5 Gbps (SStatus 113 SControl 310) 
ata1.00: failed to IDENTIFY (I/O error, err_mask=0x100) 
ata1: SATA link up 1.5 Gbps (SStatus 113 SControl 310) 
ata2: SATA link down (SStatus 0 SControl 300) 
```
/dev里面也没有看到，换回新的1T硬盘，OK。原来是个比较老的硬盘，所以ahci可能支持的不是很完整。 

CONFIG_USB_ADVANCED=y必须要打开，否则rules/busybox.mk不会包含hdparm命令。 

After this, dmesg say some on SATA and find my hard-disk. But didn't find it in /dev/ fs. If I enbale "USB Storage Support" in "Service->USB" and add SATA support in kernel, /dev/sda can get out. But after add SATA support, boot normally at first time. Second time, kernel error: 

VFS: Mounted root (squashfs filesystem) readonly on device 31:2. 

Freeing unused kernel memory: 240K (c0386000 - c03c2000) 

start service 

starting Architecture code for northstar 

SQUASHFS error: lzma returned unexpected result 0x1 

SQUASHFS error: Unable to read page, block 2bfdc, size 983a 

Kernel panic - not syncing: Attempted to kill init! exitcode=0x0000000b 

CPU1: stopping 

After I remove it, flash by CFE web ui. work again. ==> need mark the module as "M". Maybe "y" would lead to big vmlinuz.lzma and exceed the "kernel" partition? 

If I enable "USB disk" in web ui, then find hardisk but system reboot: 
```
ata1.00: CFA: FC-1307 SD to CF Adapter V1.1, Rev 1.1, max UDMA/100 
ata1.00: 7741440 sectors, multi 1: LBA 
ata1.00: configured for UDMA/100 
scsi 0:0:0:0: Direct-Access     ATA      FC-1307 SD to CF Rev  PQ: 0 ANSI: 5 
sd 0:0:0:0: [sda] 7741440 512-byte logical blocks: (3.96 GB/3.69 GiB) 
sd 0:0:0:0: [sda] Write Protect is off 
sd 0:0:0:0: [sda] Write cache: disabled, read cache: enabled, doesn't support DPO or FUA 
sda: sda1 sda2 sda3 sda4 
sd 0:0:0:0: [sda] Attached SCSI disk 
ata2: SATA link down (SStatus 0 SControl 300) 
insmod: ahci_imx.ko: module not found   insmod by sysinit/sysinit.c/start_drivers 
insmod: mmc_core.ko: module not found 
insmod: mmc_block.ko: module not found 
insmod: sdhci.ko: module not found 
insmod: sdhci-pltfm.ko: module not found 
insmod: sdhci-esdhc-imx.ko: module not found 
watchdog watchdog0: watchdog did not stop! 
Sending SIGTERM to all processes  rc/init.c 
Sending SIGKILL to all processes 
```
Who send SIGTERM to rc/init.c? [Change](http://www.linuxprogrammingblog.com/code-examples/sigaction) code(use sigaction) to check who send. Get this: 

Sending PID: 1130, UID: 0   (resetbutton process) 

Terminated.................................... 

Receiving restore command from resetbutton ... 

I check rc/resetbutton. If system detect reset button pressed, it will do reboot. But I didn't configure RESET GPIO. As a work around, set "resetbutton_enable=0" 

After reboot twice, all "new" nvram value clean and stop keep rebooting. 

src/router/services/services/usb_hotplug_adv.c, it will check nvram setting and if enable it will insmod all need modules. 

I also add sysprintf("hdparm -S 120 %s", path); to make hard disk sleep in the C file. 

Then mount. Another issue. I have to insmod "fat" then "vfat" at first. Then get error "FAT-fs (sda1): codepage cp437 not found". <http://wiki.openwrt.org/doc/howto/storage> Need "insmod nls_cp437.ko nls_iso8859-1". dmesg will give details. Kernel has include it(menuconfig->File system->Native language support). 

Not I can mount, but If I open samba3 service, kernel panic: 

​        Invalid multicall command, available commands: smbd nmbd 

root@DD-WRT:~# [APPLY] NAS (null) (null) 

submit_button=[NAS] submit_type=[save] 

name=[NAS] type=[save] service=[nassrv] sleep=[1] action=[5] 

deadlock detected, try to fix it 

signalling USER1 

waiting for services to finish (1)... 

deadlock detected, try to fix it 

root@DD-WRT:~# nvram show|grep samba will get "samba3_enable=0". If I set to "1", error happended 

<http://www.dd-wrt.com/wiki/index.php/Samba3> build samba3 separately to check if there is error?  

Then I can't access the samba. I just can see the samba name. 

<http://www.dd-wrt.com/phpBB2/viewtopic.php?p=872747> iptables configuration related 

But soon I found mine only had "nmbd" process and missed "smbd" process. 

1249 root      2100 S    /usr/sbin/nmbd -D --configfile=/tmp/smb.conf 

2032 root      2312 S    /usr/sbin/smbd -D --configfile=/tmp/smb.conf 

In services/services/samba3.c  

\#ifdef HAVE_SMP (enable in services/Makefile) 

​        eval("/usr/bin/taskset", "0x2", "/usr/sbin/smbd", "-D", "--configfile=/tmp/smb.conf"); 

\#else 

​        eval("/usr/sbin/smbd", "-D", "--configfile=/tmp/smb.conf"); 

But I have no "taskset" command. That is a feature of busybox. rules/busybox.mk will check if SMP defined. Add "CONFIG_SMP=y" to src/router/.config. will it lead to big issue? why services enable it? 

But now has 3 process.... 

 1265 root      2312 S    /usr/sbin/smbd -D --configfile=/tmp/smb.conf 

1285 root      2112 S    /usr/sbin/nmbd -D --configfile=/tmp/smb.conf 

1365 root      2476 S    /usr/sbin/smbd -D --configfile=/tmp/smb.conf 

### hard disk temperature

smartctl <http://www.smartmontools.org/>   

CrossCompile SmartMonTools <http://www.friendlyarm.net/forum/topic/4484>  

./configure --host=arm-openwrt-linux-muslgnueabi <http://josephlo.wordpress.com/2011/04/26/installing-rrdtool-on-buffalo-quad-pro/>   

smartctl -A /dev/sda | grep 194 | cut -d: -f3 | awk '{print $10}'  

smartctl -s standby,now /dev/sda

after trip, size is 400KB. 

hddtemp <http://www.guzu.net/linux/hddtemp.php>  

./configure --host=arm-openwrt-linux --build=arm-openwrt-linux CC=arm-openwrt-linux-gcc --target=arm-openwrt-linux , modify configure, remove #(eval $ac_try) 2>&5 which will run arm execute on x86. After trip, the program is 58.2KB. DB is 29K. 

When hd sleep, the output of hddtemp will have some special character which will STOP grep/sed/awk version of DD. 

services/services/usb_hotplug.c already set spindown/sleep time. Add same code to usb_hotplug_adv.c. 

\3. Web里面看不到磁盘信息 

   kromo/dd-wrt/USB.live.asp -> httpd/visuals/dd-wrt.c, ej_show_usb_diskinfo method -> no /tmp/disktype.dump and /tmp/parttype.dump ->services/services/usb_hotplug_adv.c 

kernel module load by services/sysinit/sysinit.c. But usb_add_ufd was commented out and say "Mounting is done by hotplug event". services/sysinit/devinit.c will start hotplug2.  Find code and usb_hotplug_adv.c is most possible place to call disktype. But who call usb_hotplug_adv.c? start_hotplug_block will get env from hotplug. usb_process_path will print info to /tmp/disk/sda. K2 will write to /tmp/disktype. ==> update httpd/visuals/dd-wrt.c from github. Later have use new way. 

Work beside a missed new translation 'usb.usb_diskspace'.==> change kromo/dd-wrt/lang_pack/*.js file. Later it will be compiled into httpd/www file. 

There is a command "disktype" which will print disk info.  

<http://nano-chicken.blogspot.com/2010/01/linux-modules111-sysfs-and-hotplug.html> kernel will invoke /sbin/hotplug command. But how hotplug call usb_hotplug_adv? Find "block" but didn't find rule. 

### hang on xunlei

after 10 min(I have set hd sleep time. 10min), router hang. Change to USB flash disk, works well. Write many big file to samba. took 16 min. works well too.  

----------1T hd. default. wifi off. when freeze(10 min?)----------------- 

Mem: 216968K used, 38804K free, 0K shrd, 408K buff, 177216K cached 

CPU0:  8.9% usr  9.1% sys  0.0% nic 69.3% idle  1.3% io  0.0% irq 11.1% sirq 

CPU1: 32.4% usr 11.1% sys  0.0% nic 55.1% idle  1.1% io  0.0% irq  0.0% sirq 

Load average: 1.23 1.04 0.59 3/60 2943 

  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND 

2028  2027 root     S    11880  4.6   0 23.0 {EmbedThunderMan} /tmp/mnt/sda1/xu 

2030  2027 root     S    11880  4.6   0  4.8 {EmbedThunderMan} /tmp/mnt/sda1/xu 

2029  2027 root     S    11880  4.6   0  2.9 {EmbedThunderMan} /tmp/mnt/sda1/xu 

2057  2027 root     S    11880  4.6   0  0.2 {EmbedThunderMan} /tmp/mnt/sda1/xu 

2032  2027 root     S    11880  4.6   1  0.0 {EmbedThunderMan} /tmp/mnt/sda1/xu 

2027  2024 root     S    11880  4.6   1  0.0 {EmbedThunderMan} /tmp/mnt/sda1/xu 

2024     1 root     S    11880  4.6   1  0.0 {EmbedThunderMan} /tmp/mnt/sda1/xu 

2031  2027 root     S    11880  4.6   1  0.0 {EmbedThunderMan} /tmp/mnt/sda1/xu 

------------use taskset. when freeze(after 19min) 

Mem: 223692K used, 32080K free, 0K shrd, 744K buff, 188200K cached 

CPU0:  0.0% usr  0.7% sys  0.0% nic 84.6% idle  5.7% io  0.0% irq  8.7% sirq 

CPU1: 23.9% usr 17.3% sys  0.0% nic 56.3% idle  2.3% io  0.0% irq  0.0% sirq 

Load average: 0.96 1.12 0.79 4/60 2298 

  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND 

2041  2040 root     R    12432  4.8   1 13.4 {EmbedThunderMan} /tmp/mnt/sda1/xu 

2042  2040 root     S    12432  4.8   1  4.9 {EmbedThunderMan} /tmp/mnt/sda1/xu 

2043  2040 root     S    12432  4.8   1  0.6 {EmbedThunderMan} /tmp/mnt/sda1/xu 

2070  2040 root     S    12432  4.8   1  0.0 {EmbedThunderMan} /tmp/mnt/sda1/xu 

2045  2040 root     S    12432  4.8   1  0.0 {EmbedThunderMan} /tmp/mnt/sda1/xu 

2040  2038 root     S    12432  4.8   1  0.0 {EmbedThunderMan} /tmp/mnt/sda1/xu 

2044  2040 root     S    12432  4.8   1  0.0 {EmbedThunderMan} /tmp/mnt/sda1/xu 

2038     1 root     S    12432  4.8   1  0.0 {EmbedThunderMan} /tmp/mnt/sda1/xu 

1105     1 root     S     2668  1.0   0  0.0 httpd -p 80 

------------ /proc/interrupts  

​          CPU0       CPU1 

27:         12          0       GIC  mpcore_gtimer 

29:      95912      95912       GIC  twd (keep growing) 

32:          0          0       GIC  L2C 

111:          0          1       GIC  ehci_hcd:usb1, ohci_hcd:usb2  

112:          0          1       GIC 

117:      13898          0       GIC  serial 

163:          0          5       GIC  eth1 (2.5g wifi) 

169:          0          2       GIC  eth2 (5g wifi) 

175:       7877          0       GIC  ahci 

179:     908673          0       GIC  eth0 

IPI0:         0          0       CPU wakeup interrupts 

IPI1:         0          1       Timer broadcast interrupts 

IPI2:    172363     342576       Rescheduling interrupts 

IPI3:         0          0       Function call interrupts 

IPI4:        50         26       Single function call interrupts 

IPI5:         0          0       CPU stop interrupts 

Err:          0 

<http://www.cnblogs.com/Bozh/archive/2013/01/17/2864201.html>  

After "echo 2 > /proc/irq/175/smp_affinity", ahci's interrupt will grow up only on CPU1. ehci_hcd smp_affinity is set to 2 in sysinit-northstar.c. So usb disk works well?!! So serial/uart freeze? 

After change smp_affinity of ehci to 2 & taskset xunlei to CPU1, looks working stable now. 

-----------------------After 30min, now 

​           CPU0       CPU1 

27:         12          0       GIC  mpcore_gtimer 

29:     208068     208067       GIC  twd 

32:          0          0       GIC  L2C 

111:          0          1       GIC  ehci_hcd:usb1, ohci_hcd:usb2 

112:          0          1       GIC 

117:      13978          0       GIC  serial 

163:          0          6       GIC  eth1 

169:          0          5       GIC  eth2 

175:        659      16739       GIC  ahci 

179:    2259430          0       GIC  eth0 

IPI0:         0          0       CPU wakeup interrupts 

IPI1:         0          1       Timer broadcast interrupts 

IPI2:    415314     814307       Rescheduling interrupts 

IPI3:         0          0       Function call interrupts 

IPI4:        54         26       Single function call interrupts 

IPI5:         0          0       CPU stop interrupts 

Err:          0 

Q1: ahci&eth0 smp_affinity is 3. why only grow on CPU0? 

Q2: ahci's interrupt number will grow up fast if download is fast. But looks not very big. Think MIPS has only 1 core. And why wait 10 minutes to hang? 

But after nearly 1 hours, router hang again.  

Change 1T HD to 64G with smp_affinity+taskset, >30min. 64G directly run, 2h. Not relevant with smp_affinity? just for performance? 

So should test with wifi open on since it also take interrupt? 

dmesg | grep -i sata, for Sumsung, it report as: 

ata1: SATA max UDMA/133 abar m512@0x48000000 port 0x48000100 irq 175 

ata2: SATA max UDMA/133 abar m512@0x48000000 port 0x48000180 irq 175 

ata1: SATA link up 6.0 Gbps (SStatus 133 SControl 300) 

ata2: SATA link down (SStatus 0 SControl 300) 

Then stock fw has: 

ata1: FORCE: PHY spd limit set to 3.0Gbps 

ata1: SATA max UDMA/133 irq_stat 0x00400040, connection status changed irq 175 

ata1: SATA link up 3.0 Gbps (SStatus 123 SControl 320) 

ata1.00: ATA-8: ST1000LM024 HN-M101MBB, 2BA30001, max UDMA/133 

ata1.00: 1953525168 sectors, multi 0: LBA48 NCQ (depth 31/32), AA 

ata1.00: configured for UDMA/133 

Most code need add "libata.force=3.0Gbps" to kernel command line. But mine don't work. insmod provided by busybox and called in services/sysinit/sysinit.c. I tried rmmod libata and insmod libata force="3.0Gbps" in ssh cmd. Works. So busybox didn't honor kernel CMDLINE?  

But after 20min(directly run), hang again. Change to 64G HD, now: 

ata1: FORCE: PHY spd limit set to 3.0Gbps 

ata1: SATA max UDMA/133 abar m512@0x48000000 port 0x48000100 irq 175 

ata1: SATA link up 1.5 Gbps (SStatus 113 SControl 320) 

ata1.00: ATA-7: HTS541060G9SA00, MB3IC60R, max UDMA/100 

ata1.00: 117210240 sectors, multi 0: LBA48 

ata1.00: configured for UDMA/100 

need limit speed again? 

64G HD 这个是5400转的60G的SATA1.5硬盘，日立Travelstar 5K100系列的，单片40G，双物理磁头，8M缓存，内部传输率493Mbits/s，平均寻道时间12ms。 

1T HD Capacity : 1TB , Interface : SATA 3.0 Gbps (1.5Gbps) , Buffer DRAM Size : 8 MB , Rotational Speed : 5,400 RPM class  

After set to "libata.force=1.5Gbps", looks now stable. Samba copy big file speed is 23MB/S. slow? Stop xunlei, now is 28MB/S. 

xunlei(EmbedThunderMan) looks will bind process which use most cpu to core 2. 

Write speed is 37MB/S. >copy speed?!!  

创见（Transcend）暗黑骑士系列 抗震高速移动硬盘 USB3.0 1TB （25A3K） , windows 8.1, write big file 113MB/s. When eject usb interface in Windows, spin down immediately. Push the "quick reconnect" button, Windows get the disk immediately. cool. 

After updated to new DD source, Samba write is 30MB/s, but read is only 10MB/s. 6Gbsp & 3Gbsp SATA mode all had this issue. "hdparm -t /dev/sda" get 120MB/s. src/router/services/services/samba3.c But looks working well after 4 day uptime. xunlei still freeze in 3Gbsp mode. 