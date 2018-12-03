---
layout: post
title:  "DD-WRT MiWiFi PWM Fan"
date:   2014-8-30 14:20:33
typora-root-url: ../../../blog
---

* <http://wiki.openwrt.org/doku.php?id=oldwiki:port.gpio>
* <https://forum.openwrt.org/viewtopic.php?id=36561>
* <http://wiki.openwrt.org/doc/hardware/port.gpio>

Run "gpio poll 17". It show value changing when press RESET. Then check "/src/router/rc/resetbutton.c". 

<http://www.dd-wrt.com/wiki/index.php/Netgear_R6300v2#NO_30-30-30_Reset_on_BCM4707.2F4708.2F4709_ARM_Devices>

rc/gpiowatcher, rc/gpio 

/sys/class/gpio/hc595(defined at arch/arm/mach-brcm-hnd/gpio.c)  

about 595, a good guide <http://www.arduino.cc/en/Tutorial/ShiftOut>  

./arch/arm/mach-brcm-hnd/gpio.c:        create sysfs,        printk(KERN_EMERG "Netgear R7000 init\n"); 

./brcm/arm/shared/siutils.c, si_gpioouten, set_gpio 

no /sys/class/gpio/export file 

For DD, no need export file and just execute "gpio enable 1" like <http://www.dd-wrt.com/wiki/index.php/LED_Scripts> ? 

When run "nvram commit", I saw led blink blue. It is a clue. 

In src/router/nvram/nvram_linux.c nvram_commit, it execute "system("/sbin/ledtool 1");". 

In src/router/rc/ledtool.c,  "1" means blink once. then invoke led_control(LED_DIAG, LED_ON); 

led_control defined at libutils/utils.c, LED_DIAG defined at shared/utils.h( real value is libutils/utils.c?) 

set_bit defined at libutils/gpio.c, it operate on "/dev/gpio/outen"(direction) & "/dev/gpio/out"(value). Then kernel(./brcm/arm/shared/siutils.c) will be invoked. 

"gpio(libutils/gpio.c) enable 259 (0x103) or 41" will full turn fan. Because it will goto "set_hc595(pin - 40, value);". I used "gpiowatcher -g 259" but can't find changes. 

set_hc595 defined at linux/brcm/arm/shared/siutils.c. Get clue from the file and found "gpio enable 8" will get full fan speed. But how a GPIO(1 or 0) output PWM signal(0~255)? 

Software PWM? [https://dev.openwrt.org/browser/trunk/target/linux/generic/files/drivers/pwm/gpio-pwm.c?rev=35328&order=name](https://dev.openwrt.org/browser/trunk/target/linux/generic/files/drivers/pwm/gpio-pwm.c?rev=35328&amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;order=name)  

In miwifi rootfs, there is a "usr/sbin/fan_tmp_ctrl“ &  usr/sbin/fan_tmp.sh & etc/init.d/fantmp. 

. ./linux-2.6.36/arch/arm/plat-brcm/shared/siutils.c has method to set GPIO. But I am not sure. 

use "strings fan_tmp_ctrl" get "echo "0 1 20 3" > /proc/pwm/config". 

It has a TMP75  Digital Temperature Sensor. It used I2C bus. Use i2cdetect to get information of it? lm-sensors has supported it. <http://www.lm-sensors.org/wiki/Devices> It also has TI TPS5432, STEP DOWN CONVERTER, why use it? 

Stock dmesg has(3 channel total): 
```
GPIO PIN MUX: 
dmu_cru_gpio_control0:  PWM channel 0 func on 
ccb_gpio_aux_sel:       PWM channel 0 func on 
PWM channel 0 config: 
prescale:       1 
periodCnt:      20 
dutyHiCnt:      20 
outputEnable:   Enabled 
```
I copy 3.5's gpio-pwm to 3.10 of dd kernel. 3.10 has remove gpio-pwm. But get error: 
```
gpio_pwm: Unknown symbol pwm_unregister (err 0) 
pwm: Unknown symbol device_unregister (err 0) 
pwm: Unknown symbol put_device (err 0) 
pwm: Unknown symbol __class_register (err 0) 
pwm: Unknown symbol device_create (err 0) 
pwm: Unknown symbol sysfs_create_group (err 0) 
pwm: Unknown symbol __alloc_workqueue_key (err 0) 
pwm: Unknown symbol class_find_device (err 0) 
```
Why "make modules" didn't give any error?After add GPL to 3.5 gpio-pwm.c, above issue gone. But but it have to accept platform_data to get gpio info. 

 <https://forum.openwrt.org/viewtopic.php?id=42079> has same issue and has a patch.  

<http://www.right.com.cn/forum/thread-142822-3-1.html> patch working. It use configfs(stock fw didn't use it) to get gpio info. But  
```
root@DD-WRT:~/config/gpio_pwm# mkdir 8 
mkdir: can't create directory '8': Invalid argument 
The module can't get control of gpio 8 because I can't "echo 8 > /sys/class/gpio/export"? Try to add log at "ret || !gpio_is_valid(gpio)" in gpio-pwm.c. Code stop at here: 
ret = strict_strtoul(name, 10, &gpio); 
if (ret || !gpio_is_valid(gpio)) { 
make_gpio_pwm_target---- 
```
3.5 didn't use this way. So turn to 3.5 code? Add register to soc_add_devices of arch/arm/plat-brcm/bcm5301x.c. After modified code, get "gpio_pwm: probe of gpio_pwm.0 failed with error -22" error. Should use siutils.c? And change gpio_direction_output to si_gpioouten+si_gpioout or just set_gpio(siutils.c, only si_gpioout)? Need below step to configure(<https://sites.google.com/site/bifferboard/Home/pwm-gpio> ): 
```
cat request 
echo 10000000 > period_ns 
echo 5000000 > duty_ns 
echo 1 > polarity 
echo 1 > run 
```
But after few second, fan get full speed then back. CPU usage? By siboqi, when full speed, the duty is "50-50-50". SMP? I add "smp_processor_id" and it always return 1. 

It use High Precision Event Timer( <http://www.ibm.com/developerworks/cn/linux/l-cn-timerm/> ), init by "hrtimer_init(&gp->t, CLOCK_MONOTONIC, HRTIMER_MODE_REL);". gpio_direction_output do the real set_gpio operation. 

RPi(bcm2835) hardware PWM <http://hertaville.com/2014/07/07/rpipwm/> it use /dev/mem & mmap to write soc register. 

adafruit also provide a kernel [module](https://github.com/adafruit/adafruit-raspberrypi-linux/blob/rpi-3.10.y/drivers/misc/rpi-pwm.c) in it's [Occidentalis](https://learn.adafruit.com/adafruit-raspberry-pi-educational-linux-distro/occidentalis-v0-dot-2) distribution. But it looks no difference with above nmap way.  

PWM by DMA <http://pythonhosted.org/RPIO/pwm_py.html> on any GPIO. Comments show that it was driven by timer PLLD 

theory <http://documentation.renesas.com/doc/products/mpumcu/apn/reu05b0093_m16cap.pdf>  

Broadcom Kona(bcm28155) PWM driver <http://lwn.net/Articles/592310/> exit at git://git.kernel.org/pub/scm/linux/kernel/git/arm/arm-soc.git (on my dreamhost). 

<http://lwn.net/Articles/596601/> Add Allwinner SoCs PWM support. Looks complex. 

S5P PWM code look [here](https://android.googlesource.com/device/ti/bootloader/uboot/+/11c8c96d1cb9f1afef03ff496880945ada7c3d62/arch/arm/cpu/armv7/s5p-common/pwm.c). 

linux-3.10/brcm/arm/include/sbchipc.h has lots of PWM definition. 

WRT1900AC also has fan. It use ioremap as RPi. <https://github.com/jimmychungbelkin/Mamba/blob/master/barrier_breaker/target/linux/mvebu/patches-3.10/0313-ARM-mvebu-fan-monitor.patch>  

Some user find after several days, ksoftirqd (sirq)take 40% CPU. It would lead by this software PWM. 

arch/arm/plat-brcm/bcm5301x_dmu.c has provide many clock, which would be used for PWM. 

1. was it enabled at default? Right. Has _dmu*.o file. 

2. RPi can just use nmap to set PWM. But how to set the output pin? dsti as sunxi?(arch/arm/boot/dts/bcm2835-rpi-b.dts did say that) So RPi didn't need a separate driver? WRT1900AC looks using same way?  

<https://github.com/torvalds/linux/blob/master/arch/arm/boot/dts/bcm5301x.dtsi> it is base dts file for BCM4708. 

./include/plat/plat-bcm5301x.h:#define  SOC_DMU_BASE_PA         0x1800c000     /* Power, Clock controls */ 

in ddwrt, 

board_init_timer(.init_time, arch/arm/mach-brcm-hnd/board_ns.c) 

  soc_init_timer 

​    soc_dmu_init (arch/arm/plat-brcm/bcm5301x_dmu.c) 

​    soc_cru_init (arch/arm/plat-brcm/iproc_cru.c) 

in bcm5301x, uart was added on iproc_slow_clk. dmesg can prove this. iproc means "Broadcom iProc common SoC platform". 

arch/arm/plat-brcm/iproc_cru.c 
```
/* bcm5301x_dmu.c 
 \* chan 0 - Ethernet switch and MAC, RGMII, need 250 MHz 
 \* chan 1 - Ethernet switch slow clock, 150 Mhz 
 \* chan 2 - USB PHY clock, need 30 MHz 
 \* chan 3 - iProc N MHz clock, set from OTP 
 \* chan 4 - iProc N/2 MHz clock, set from OTP 
 \* chan 5 - iProc N/4 MHz clock, set from OTP  
 \* 
 */ 
```

http://www.gigamegablog.com/2012/03/16/beaglebone-coding-101-buttons-and-pwm/>  

[http://processors.wiki.ti.com/index.php/AM335x_PWM_Driver's_Guide](http://processors.wiki.ti.com/index.php/AM335x_PWM_Driver's_Guide)

 <https://github.com/beagleboard/linux/blob/3.14/drivers/pwm/pwm-tiecap.c>  

But I still find no clue on which pin/gpio was set as PWM target/output. Fixed? So I just need to enable it? 

In RPi, 

clocksource_of_init(.init_time, arch/arm/mach-bcm2835/bcm2835.c) no use? 

bcm2835_init_clocks(drivers/clk/clk-bcm2835.c), from it's pdf, the PWM didn't need software to driver? 

Odroid C1 PWM 

<http://odroid.com/dokuwiki/doku.php?id=en:c1_hardware_pwm>

<https://github.com/hardkernel/linux/blob/odroidc-3.10.y/drivers/amlogic/pwm/pwm-meson.c>  

Looks the code is much specific to platform. 

https://bitbucket.org/tsynik/tomato-arm/commits/branch/shibby-arm>

<https://bitbucket.org/tsynik/tomato-arm/commits/0ffa798d26bcfab872b426e491e9b10777629062>  

Not sure it was software PWM or not. It use 'setitimer'. 

在命令提示符下： 
```
nvram set fanctrl_dutycycle=1~5 
nvram commit 
```
来调整1和2等级是安静的，从3开始就有声音，5的燥音就基本很大了。 

To start it at boot, I add it in rc/init.c. Need to use FORK(eval("phy_tempsense"));. sysprintf will block boot process. 

1. when reboot, fan is very noise. 

2. how to let it run at auto-mode? looks the 1~5 is reversed when in auto-mode. 

https://github.com/RMerl/asuswrt-merlin/blob/master/release/src/router/shared/shared.h> has defined: 

\#ifdef RTAC5300 

RPM_FAN, /* use to control FAN RPM (Hi/Lo) */ 

\#endif 

RTAC5300 is quite new. It used BCM8709. Not sure it has fan or not.  