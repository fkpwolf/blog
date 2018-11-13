---
layout: post
title:  "DD-WRT MiWiFi Firmware Build"
date:   2015-03-26 16:27:00
---
### 1. Prepare  
Ubuntu Server 14.04. `sudo apt-get install build-essential ccache flex bison texinfo gettext pkg-config mtd-utils cmake autoconf`. 
Goto `src/router` dir. Run `cp configs/northstar/.config_northstar_mini .config`. Change LINUXDIR if necessary. Copy toolchain from openwrt/staging-dir if you have build firmware for broadcom ARM SOC, or download from [here](ftp://bo.mirror.garr.it/pub/1/openwrt/snapshots/trunk/bcm53xx/OpenWrt-Toolchain-bcm53xx-for-arm_cortex-a9-gcc-4.8-linaro_uClibc-0.9.33.2_eabi.tar.bz2). Set env:
```
export PATH=~/toolchain-arm_cortex-a9_gcc-4.8-linaro_uClibc-0.9.33.2_eabi/bin:$PATH
export CROSS_COMPILE=arm-openwrt-linux-
export ARCH=arm
export STAGING_DIR=~/dd-wrt-staging
```
Run `source mi.sh` set env.
musl-gcc: openwrt has a draft support for it(need open "Show broken platforms/packages" in menuconfig). "ln -sf /lib/libc.so ld-musl-mips.so.1"(./package/libs/toolchain/Makefile to ignore ld-musl-*). Package need be modified to support musl. After change gcc, need run "realclean&configure" again. 
For 4.9 GCC, check [this patch](http://patchwork.openwrt.org/patch/5735/) and other 2. Need remove "mips" part in 200-musl.patch. Need add "CFLAGS=-fno-toplevel-reorder" to "toolchain/musl/common.mk". Now(8.25) openwrt has gcc4.8+musl1.1.4 and didn't need patch any more.
Or directly download from ftp://ftp.dd-wrt.com/toolchains/toolchains.tar.gz . Obviously later is better.

### 2. Configure 
make -f Makefile.northstar configure. For a specific package, make -f Makefile.northstar zabbix-configure.
* ld: cannot find -lnvram ... ==>"make nvram jansson-configure jansson utils" at first.
* Can't find "revision.h" ==> create file and cp to src/router/shared, src/router/libutils, `src/router/httpd/visuals, and src/router/httpd.`
`echo -n '#define SVN_REVISION "' > revision.h `
`svnversion -n . >> revision.h `
`echo '"' >> revision.h `
For git, use 'git rev-list --count HEAD' as http://stackoverflow.com/questions/4120001/what-is-the-git-equivalent-for-revision-number
* checking host system type... mipsel-unknown-linux-gnu when compile glib for nocat ==>Edit glib/config.sh to replace all "mips" to "arm" and remove "mips" flag.
* checking for capable lex... insufficient: apt-get install flex; yacc: Command not found: apt-get install bison
*  ./configure: line 4624: syntax error near unexpected token `DAEMON,' `PKG_CHECK_MODULES(DAEMON, libdaemon)' in radvd ==> comment it out with #.
* libiconv error when configure zabbix ==> disappear after do glib20-configure at first(even failed neither). "-lz" not found. do "zlib-configure & zlib" at first.
* cp .libs/libiconv.so preloadable_libiconv.so cp: cannot stat libs/libiconv.so: No such file or directory when do glib20-configure==>arm-linux-uclibc-ld missed. So no *.so was generated. Check config.log!
* automake-1.13' is missing on your system.==>Mine Ubuntu(14.04) default version is 1.14. To downgrade, visit this page https://launchpad.net/ubuntu/trusty/+package/automake. Download .deb file and install it(sudo dpkg -i DEB_PACKAGE). Another way: uninstall stock automake at first, then compile it by yourself.
```
wget http://ftp.gnu.org/gnu/automake/automake-1.13.4.tar.gz
tar xzfv automake-1.13.4.tar.gz
cd automake-1.13.4
./configure
make install
```
* localename.c:2617:31: error: dereferencing pointer to incomplete type,  [glib20-configure], when use dd-wrt's musl gcc==>glib20/gettext/gettext-runtime/gnulib-lib/localename.c, type is locale_t(glibc type). Comment out line to pretend glibc<2(__GLIBC__ in my Openwrt GCC is undefined but dd-wrt's is 2. Have to write code to [print](http://stackoverflow.com/questions/1562074/how-do-i-show-the-value-of-a-define-at-compile-time) the macro. [Dump](http://stackoverflow.com/questions/2224334/gcc-dump-preprocessor-defines) of predefined macros didn't contain it). Then get error "undefined reference to `gnu_basename'", use as [this topic](http://gnu.utils.bug.narkive.com/DPCiJYF2/error-building-sharutils-with-other-libc). Then more and more issue...give up. Just comment out the undefined type, because my Openwrt gcc also didn't have that type.
* You must have either have gettext support in your C library, or use the GNU gettext library. 
configure:8700: result: yes
configure:8737: checking for msgfmt
configure:8767: result: no
==>check glib20/libglib/config.log. "apt-get install gettext".
* use musl-gcc and  "make glib20-configure", get error:
configure.ac:25: error: version mismatch.  This is Automake 1.13.3,
configure.ac:25: but the definition used by this AM_INIT_AUTOMAKE
configure.ac:25: comes from Automake 1.14.1.  You should recreate
configure.ac:25: aclocal.m4 with aclocal and run automake again.
WARNING: 'automake-1.13' is probably too old.
==>"rm glib20/gettext/aclocal.m4" to let re-generate it. Then I need "sudo apt-get install gperf", WHY? this not need when using gcc-uClibc?
* use musl-gcc and "make -f Makefile.northstar libubox", get error:
Linking C executable jshn
/home/fan/dd-wrt-git2/src/router/_staging/usr/lib/libjson.so: undefined reference to `__ctype_b'
==>make will link libjson which has build with uClibc gcc. But '__ctype_b' was removed by musl gcc. To fix it, run "make json-c" to rebuild the static lib at first. Sometimes need to remove *.o, *.so, *.la file. "make realclean" will fix all these issues.
* error: possibly undefined macro: AM_NLS when dotransmission-configure=>copy /usr/share/aclocal/nls.m4 to /usr/local/share/aclocal/nls.m4.
* lzo enabled but missing when do openvpn-configure => make realclean didn't work.
* gcc: error: unrecognized command line option '-fno-plt' when do dropbear => dropbear was updated. Change to GCC 5.3 downloaded from Openwrt bcm53xx SDK

### 3. Compile 
run `make -f Makefile.northstar all -j8` to compile all packages. To build  single package, use like `make mstp`. To build kernel, `make kernel`.
* make[1]: *** No rule to make target `/home/fan/dd-wrt/src/router/register/register_check.o', needed by `libutils.so'.  Stop.=>ref by libutils/Makefile. Remove CONFIG_SUPERCHANNEL=y,
* cp: cannot stat style No such file or directory, when "make www" ==>vi kromo/dd-wrt/Makefile to comment out "cp -a style $(INSTALLDIR)/www” or add ‘-‘ at head of line to bypass this step.
* make[1]: repugly: Command not found when "make httpd" ignored since it started with "-". ==>  vi kromo/dd-wrt/Makefile, remove optimizefiles.
* linux/mtd/mtd-abi.h: No such file or directory when make rc package ==>change to mtd/mtd-abi.h(GCC has this file) in rc/mtd_main.c & rc/mtd.c. But when I used gcc 5.3, after apply above patch, get `error has no member named 'oobblock'`. Now I copy linux/mtd dir from gcc 4.8. Do same for netfilter_ipv4 & netfilter_ipv6. So DD guys did same patch for Openwrt GCC toolchains?
* In file included from networking/libbridge.h:26:0 which from networking/mssid.c:30:
/home/fan/toolchain-arm_cortex-a9_gcc-4.8-linaro_uClibc-0.9.33.2_eabi/include/linux/if_bridge.h:183:20: error: field 'ip6' has incomplete type ==>~~[services/networking/mssid.c, bridge/libbridge/libbridge_devif.c, bridge/libbridge/libbridge_if.c, bridge/libbridge/libbridge_init.c, bridge/libbridge/libbridge_misc.c, bridge/brctl/brctl.c, bridge/brctl/brctl_cmd.c, bridge/brctl/brctl_disp.c,~~ mstp/bridge_track.c, mstp/mstp.c, mstp/ctl_functions.h], put in.h before libbridge.h
```
#include <netinet/in.h> //fan add
#include "libbridge.h"
#include <net/if.h>
```
//#include <netinet/in.h> //fan delete
==>
Use MUSL GCC, should do as "services/networking/libbridge.h" to add "in.h". Then just need change "bridge/libbridge/libbridge.h"
* extensions/libipt_connlimit.c:9:47: fatal error: linux/netfilter_ipv4/ip_conntrack.h: No such file or directory ==> vi iptables/extensions/Makefile to delete all failed extensions. This file exist in ddwrt's toolchain but the toolchain give lost of error when compile glib20. Another fix ~~The header file exist in old kernel. Use new iptables in ~/openwrt/build_dir/target-arm_cortex-a9_uClibc-0.9.33.2_eabi/linux-bcm53xx/iptables-1.4.21? Then change "SED" path(or create a aliase) in iptables/libiptc/Makefile. Remeber clean & reconfigure at first.~~  "./configure --host=arm-openwrt-linux --target=arm-openwrt-linux"
~~when run "iptables-install" on new iptable package.install: omitting directory ==>edit rules/iptables.mk to correct path~~
* netconf ./netconf_linux.h:27:46: fatal error: linux/netfilter_ipv4/ip_nat_rule.h....vi netconf/Makefile, remvo CONFIG_NETCONF, CONFIG_UPNP(depend on netconf).
* callvalidate.o: In function `initWeb’: callvalidate.c:(.text.initWeb+0x15c): undefined reference to `F20307’==> the C invoke(dynamic) validate.so by dlsym. Use "nm callvalidate.o" can see the undefined symbol. Since validate.so was on disk and not linked into httpd, callvalidated.o should not need any symbol related with "initWeb". Change to typedef style as [this topic](http://blog.sina.com.cn/s/blog_48ebca64010005io.html) and OK. (But sometimes didn't need this fix, and "nm callvalidate.o" return "t initWeb". Strange. run "make httpd-clean" then work fine one time.)
* compile sputnik "arm-openwrt-linux-uclibcgnueabi-gcc.bin: error: src/apd.o: No such file or directory" =>remove CONFIG_SPUTNIK_APD. not GPL.
* error: redefinition of 'struct sysinfo' when build coova-chilli. =>vi coova-chilli/src/system.h, delete "#include <sys/sysinfo.h>".
* xsltproc  warning: failed to load external entity "/usr/local/share/xsl/docbook/manpages/docbook.xsl" vi rtpproxy/Makefile, change url to real path (/usr/share/xml/docbook/stylesheet/docbook-xsl/manpages/docbook.xsl). If the file missed need install docbook-xsl package.
* fatal error: execinfo.h: No such file or directory when build zabbix =>zabbix/include/config.h, delete HAVE_EXECINFO_H entry. Then No rule to make target `../../src/libs/zbxsysinfo/arm/libspecsysinfo.a', needed by `zabbix_agent'. =>Change zabbix/src/zabbix_agent/Makefile to change arm to linux. Same issue for "proftpd/config.h".
* driver_madwifi.c:23:28: fatal error: include/compat.h: No such file or directory #include <include/compat.h>. The include (src/router/madwifi.dev) is wrong => change it in hostapd2/hostapd/.config. CFLAGS +=-I$(TOP)/madwifi.dev/madwifi.dev -DNEED_PRINTF —> CFLAGS +=-I$(TOP)/madwifi -DNEED_PRINTF
* /home/fan/dd-wrt/src/router/_staging/usr/lib/libjson.so: undefined reference to `rpl_realloc' when compile libubox => In json-c/config.h.in, remove #undef malloc&realloc. In json-c/configure.in, remove AC_FUNC_MALLOC&AC_FUNC_REALLOC checking. Remember to delete  _staging/usr/lib/libjson.*. At last another error install: cannot stat ulibubox/libjson_script.so: No such file or directory. But it can be ignored.
* emf/emfconf: No such file or directory when build emf.==> this dir not exist but "emf_bin" indeed. Change dir to emf-bin. make sense? Change rules/configs.mk to name like emf222. Or comment out line: #include rules/emf.mk in rules/all.mk.
* 'aclocal-1.14' is missing on your system. when make openvpn ==>under openvpn directory, run "aclocal; automake". Then configure again: "make openvpn-conf". Basically, your makefiles were generated by Automake 1.13 and now you've got 1.14 so they're getting confused, so running automake will recreate all the Makefiles which use your new version.
* fatal error: generated/autoconf.h: No such file or directory while build opendpi ==>make kernel at first.
* cgi.c:56:11: error: 'struct hsearch_data' has no member named 'table', [httpd] ==> defined(__GLIBC__). use other if path
* getopt.c:64:27: fatal error: gnu-versions.h: No such file or directory [pptpd] ==> comment out the .h file.
* In function `initWeb': callvalidate.c:(.text.initWeb+0x14c): undefined reference to `I28020' ==> make httpd-clean.
* ./configure: line 15436: syntax error near unexpected token `libxml2,'./configure: line 15436: `PKG_CHECK_MODULES(libxml2, libxml-2.0 >= $LIBXML2_VERSION)' make: *** [ipeth-configure] Error 2. Remove it. Then get "../src/.libs/libplist.so: undefined reference to `xmlNewTextChild'" error. Defined at ./libxml2/include/libxml/encoding.h.

### 4. Generate firmware 
`make -f Makefile.northstar CROSS_COMPILE=arm-openwrt-linux- ARCH=arm  STAGING_DIR=~/dd-wrt-staging install`
* strip: Unable to recognise the format of the input file `/home/fan/dd-wrt/src/router/arm-uclibc/install/pciutils/sbin/lspci'. ==>change STRIP=-s to "STRIP=" in pciutils/Makefile
* install -m 755 airmon-ng airdriver-ng airodump-ng-oui-update /usr/sbin Permission denied. ==>vi aircrack-ng/scripts/Makefile, hard code the path. DESTDIR= ../../arm-uclibc/install/aircrack-ng
* `cp ./opt/bin/ipkg arm-uclibc/target/bin -->../../opt/bin/ipkg`, in Makefile.northstar. Same for other missed file.
* make: tools/lzma_4k: Command not found==>I download R7000 code from ftp://downloads.netgear.com/files/GPL/ and found it contains lzma_4k. Use it to build vmlinux.lzma and boot from tftp. It is 32bit. "apt-get install libc6:i386 lib32stdc++6".
* make: ./tools/trx: Command not found ==> compile /dd-wrt/opt/tools/trx.c and copy binary to tools dir.
* make: ./tools/seama/packimgs: Command not found ==> http://wiki.openwrt.org/toh/d-link/dir-645. 32 bit. Need install ia32-libs by this [guide](http://stackoverflow.com/questions/23182765/how-to-install-ia32-libs-in-ubuntu-14-04-lts/24342861). It just for D-LINK dir868. Should just need disable it in Makefile.northstar.

Above looks build the whole dd-wrt firmware, for just build kernle:
As [this topic](http://wiki.dd-wrt.de/phpBB2/viewtopic.php?p=900307&amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;sid=21fe71b57f6be9dded7a8191285217e3), go to ./src/linux/universal/linux-3.10[10~15], there is file .config_northstar_smp. Build uImage easy.

### 5. Flash firmware 
error "Loading: Image compressed with unsupported method". I have to use netgear.trx rather than K3_R7000.chk. 
Boot from tftp. Use [this way](http://wiki.jackslab.org/%E5%B0%8F%E7%B1%B3%E8%B7%AF%E7%94%B1%E5%86%85%E6%A0%B8%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA) to extract vmlinuz from vmlinuz.trx. It works for miwifi-stock.bin(from huanghelyou). After boot successfully, device will boot from flash0.os at next time. By compare result of "nvram show", there are(right is success):
```
flag_try_sys1_failed=1(=0)
flag_try_sys2_failed=1(=0)
flag_boot_type=2 (flag_boot_type=1)
flag_last_success=1(flag_last_success=0)
```
For my vmlinuz, it just read "0" byte and "Loading: PANIC: out of memory!“.
=>change Makefile.northstar to let it generate vmlinux. And generate vmlinuz.lzma, same error. I download R7000 code from ftp://downloads.netgear.com/files/GPL/ and found it contains lzma_4k. Use it to build vmlinux.lzma and boot from tftp. WORK!!! 

### 6. MTD partition
Router stop at "Digital core power voltage set to 1.0V"(only one line). Real BRICK!!! What happened? CFE broken? I just boot from tftp and didn't touch CFE. Should I disable NAND and use SPI as here?
```
Stock
reating 10 MTD partitions on "bcmsflash":
0x000000000000-0x000000040000 : "boot"
0x000000040000-0x000000340000 : "os"
0x000000340000-0x000000640000 : "os1"
0x000000640000-0x000000ed0000 : "squashfs"
0x000000ed0000-0x000000ee0000 : "crash"
0x000000ee0000-0x000000fe0000 : "overlay"
0x000000fe0000-0x000000ff0000 : "board_data"
0x000000ff0000-0x000001000000 : "nvram"
0x000000000000-0x000000fe0000 : "firmware"
0x0000008055c0-0x000000da7000 : "os2"

My FW
reating 6 MTD partitions on "bcmsflash":
0x000000000000-0x000000040000 : "boot"
0x000000040000-0x000000ff0000 : "linux"
0x000000180000-0x000000b40000 : "rootfs"
0x000000b40000-0x0000001f0000 : "ddwrt"
0x0000001e0000-0x0000001f0000 : "nvram_cfe"
0x0000001e0000-0x0000001f0000 : "nvram"

Huanghelou DD-WRT
Creating 5 MTD partitions on "bcmsflash":
0x000000000000-0x000000040000 : "boot"
0x000000040000-0x000000ff0000 : "linux"
0x000000180000-0x0000007a0000 : "rootfs"
0x0000007a0000-0x0000001f0000 : "ddwrt"
0x0000001e0000-0x0000001f0000 : "nvram"
```
my FW dmesg has: 
"found cfe nvram
check if nvram copy is required CFE Size is 65536
nvram copy magic is 81F15C7F
copy cfe nvram to base nvram
remap nvram -193508185" Write to wrong place? No this kind of message in huanghelou.
Above print from linux-3.10/arch/arm/mach-brcm-hnd/board_ns.c, init_mtd_partitions(), not to create nvram_cfe partition? RIGHT!! Now after reboot device still can enter DD. "cat /proc/mtd" return strange layout:
```
root@DD-WRT:~# cat /proc/mtd
dev:    size   erasesize  name
mtd0: 00040000 00010000 "boot"
mtd1: 00fb0000 00010000 "linux"
mtd2: 009c0000 00010000 "rootfs"
mtd3: ffffffffff6b0000 00010000 "ddwrt"
mtd4: 00010000 00010000 "nvram"
```
looks "nvram_cfe" only created once. If fw1 apply above patch, then fw2 will not need. But when upgrade from stock to fw2, error happened. And fw2 maybe reboot/restart after startup fully. Not sure if relate with this.

Another issue is after reboot, now 
```
Device eth0:  hwaddr 8C-BE-BE-20-F7-5F, ipaddr 192.168.1.1, mask 255.255.255.0
        gateway not set, nameserver not set
```
Boot program checksum is invalid.
It can boot DD, but can't flash through TFTP again.

"Kernel panic - not syncing: No init found.  Try passing init= option to kernel. See Linux Documentation/init.txt for guidance." This happen when I clone a new dd-wrt. init=/bin/sh. /bin/sh link to busybox, which depend on "ld-uClibc.so.0"(readelf -a). But at my case, the file didn't exist and there is a "ld-musl-arm.so.1". Then I found this changeset change the link target name. ==>delete CONFIG_MUSL=y

"scp fan@192.168.1.137:K3_R7000.chk ." Then falsh in DD.

After build with gcc MUSL, dd-wrt boot and stop at. 
```
starting Architecture code for northstar
BusyBox v1.22.1 (2014-07-23 16:11:07 CST) multi-call binary.
Usage: mount [OPTIONS] [-o OPTS] DEVICE NODE
Mount a filesystem. Filesystem autodetection requires /proc.

There are filesystem-specific -o flags.
starting hotplug
/etc/hotplug2-createmtd.sh: line 18: can't create /dev/null: Read-only file system
ln: /dev/mtd/0: No such file or directory
==> /dev not mount. change services/sysinit/devinit.c
//system("mount -t tmpfs none /dev -o size=512K");
 system("mount -t tmpfs -o size=512K none /dev");
And now console(serial, ssh) all looks a litter strange.
```
start service
starting Architecture code for northstar
cannot load /usr/lib/devinit_service.so
cannot load /usr/lib/sysinit_service.so
Kernel panic - not syncing: Attempted to kill init! exitcode=0x0000000b
The error come from rc/init.c when invoke start_service("devinit");  start_service("sysinit"). Looks correct. Why it didn't use busybox init? start_servies is just sysprint, which was defined at ./libutils/shutils.c:int sysprintf(const char *fmt, ...).
Once time, I changed services/sysinit/sysinit-northstart.c, rebuild, then got this error. To fix it, have to "make realclean configure all install".
dmesg say:
```
bcmsflash: squash filesystem found at block 26  (./arch/arm/mach-brcm-hnd/board_ns.c)
Creating 5 MTD partitions on "bcmsflash":     (./drivers/mtd/mtdpart.c)
0x000000000000-0x000000040000 : "boot"
0x000000040000-0x000000ff0000 : "linux" (ff0000 = 16MB)
0x0000001a0000-0x000000770000 : "rootfs"
0x000000770000-0x0000001f0000 : "ddwrt"
0x0000001e0000-0x0000001f0000 : "nvram"
RT-AC56U:
Creating 4 MTD partitions on "nflash":
0x000000000000-0x000000080000 : "boot"
0x000000080000-0x000000200000 : "nvram"
0x000000200000-0x000002000000 : "linux"
0x000000360000-0x000002000000 : "rootfs"
```
Get lots of error:
```
starting Architecture code for northstar  (rc/init.c => start_service("devinit"); =>services/sysinit/devinit.c)
SQUASHFS error: lzma returned unexpected result 0x1(fs/squashfs/inode.c)
SQUASHFS error: Unable to read fragment cache block [38c36]
SQUASHFS error: Unable to read page, block 38c36, size 7c67
```
And console looks very ugly. just "$", no host prompt. Change to old 3.10 kernel, OK. Change to 3.18 kernel. OK. brcm code should not change too much. So some fs/spi driver in new 3.10 kernel was  broken? "diff -r linux-3.10/brcm/ linux-3.18/brcm|" show many differences. "diff -r linux-3.10/brcm/include/bcmdevs.h linux-3.18/brcm/include/bcmdevs.h" you will find 3.10 use 2015 code and 3.18 use 2012 code. Copy linux-3.18/brcm to linux-3.10. Compile OK. But still get SQUASHFS error. Just copy arch/arm/plat-brcm. Same result. Copy all 3 or "mach-brcm, brcm.", stop at "Uncompressing Linux... done, booting the kernel."
disable lzma in Makefile.northstar and linux/fs/squashfs/innode.c. get "VFS: Cannot open root device "1f02" or unknown-block(31,2): error -19".

Then I found my mtd layout was strange. "nvram" has wrong starting position and overlay others.
```
if (nvram_match("boardnum", "32") && nvram_match("boardtype", "0x0665")
   && nvram_match("boardrev", "0x1301")) {//R7000
     maxsize = 0x200000; 
     size = maxsize;
} 
```
remove this, then OK.(nvarm partition should match with stock firmware?) Also after reboot, can  enter CFE console again after reboot. Now layout is:
```
Creating 5 MTD partitions on "bcmsflash":
0x000000000000-0x000000040000 : "boot"
0x000000040000-0x000000ff0000 : "linux"
0x0000001c0000-0x000000ef0000 : "rootfs"
0x000000ef0000-0x000000ff0000 : "ddwrt"
0x000000fe0000-0x000000ff0000 : "nvram"
```

wl: Unknown symbol clm_header (err 0) when "insmod wl".
http://www.dd-wrt.com/phpBB2/viewtopic.php?t=269372&postdays=0&postorder=asc&start=105
http://svn.dd-wrt.com/ticket/3972?cversion=2&cnum_hist=1 say to modify Makefile.northstar can fix this issue. Not work for me.
```
fan@bstone:~/dd-wrt-svn/src/linux/universal/linux-3.18$ readelf -Ws ./drivers/net/wl/wl.ko|grep clm_header
  4689: 00000000     0 NOTYPE  GLOBAL DEFAULT  UND clm_header
```
Still use wl.ko extracted from kong's FW at last. And didn't patch anything to make 2.5G & 5G wifi work. 

libstdc++.so.5: cannot open shared object file
1. extract http://packages.ubuntu.com/trusty/i386/libstdc++5/download and cp so file to /usr/lib32
2. export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/lib32