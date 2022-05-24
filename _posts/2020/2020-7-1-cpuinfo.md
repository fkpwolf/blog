---
layout: post
title: "cat /proc/cpuinfo"
date: 2020-7-1
typora-root-url: ../../../blog
---
```
system type : MediaTek MT7620A
machine : Baidu BR100 Board
processor : 0
cpu model : MIPS 24KEc V5.0
BogoMIPS : 385.84
wait instruction : yes
microsecond timers : yes
tlb_entries : 32
extra interrupt vector : yes
hardware watchpoint : yes, count: 4, address/irw mask: [0x0ffc, 0x0ffc, 0x0ffb, 0x0ffb]
ASEs implemented : mips16 dsp
shadow register sets : 1
kscratch registers : 0
core : 0
VCED exceptions : not available
VCEI exceptions : not available
```



```
system type : Atheros AR9344 rev 2
machine : MerCury MW4530R
processor : 0
cpu model : MIPS 74Kc V4.12
BogoMIPS : 278.93
CPUClock : 560
FlashSize : 8
wait instruction : yes
microsecond timers : yes
tlb_entries : 32
extra interrupt vector : yes
hardware watchpoint : yes, count: 4, address/irw mask: [0x0000, 0x0720, 0x0700, 0x0b98]
```


```
system type : Atheros AR9330 rev 1
machine : TP-LINK TL-WR703N v1
processor : 0
cpu model : MIPS 24Kc V7.4
BogoMIPS : 265.42
wait instruction : yes
microsecond timers : yes
tlb_entries : 16
extra interrupt vector : yes
hardware watchpoint : yes, count: 4, address/irw mask: [0x0000, 0x0f10, 0x0e10, 0x0a20]
```



```
system type : Atheros AR7240 rev 2

machine : Buffalo WHR-HP-G300N
processor : 0
cpu model : MIPS 24Kc V7.4
BogoMIPS : 265.42
```


```
Processor : ARMv7 Processor rev 2 (v7l)
processor : 0
BogoMIPS : 1785.85

processor : 1 2 3

Features : swp half thumb fastmult vfp edsp neon vfpv3 tls vfpv4 idiva idivt
CPU implementer : 0x41
CPU architecture: 7
CPU variant : 0x0
CPU part : 0xc07
CPU revision : 2
Hardware : ODROIDXU
Board Type : XU Lite
```

```
Processor : ARMv7 Processor rev 0 (v7l)

processor : 0
BogoMIPS : 1631.46
processor : 1
BogoMIPS : 1631.46
Features : swp half thumb fastmult vfp edsp neon vfpv3
CPU implementer : 0x41
CPU architecture: 7
CPU variant : 0x3
CPU part : 0xc09
CPU revision : 0

Hardware : RK30board
```

```
processor : 0 - 7
vendor_id : AuthenticAMD
cpu family : 16
model : 8
model name : AMD Opteron(tm) Processor 4122
stepping : 0
cpu MHz : 798.039
cache size : 512 KB
physical id : 0
siblings : 4
core id : 0
cpu cores : 4
apicid : 8
initial apicid : 0
fpu : yes
fpu_exception : yes
cpuid level : 5
wp : yes
flags : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov
pat pse36 clflush mmx fxsr sse sse2 ht syscall nx mmxext fxsr_opt pdpe1gb rdtscp
lm 3dnowext 3dnow constant_tsc rep_good nonstop_tsc extd_apicid pni monitor cx1
6 popcnt lahf_lm cmp_legacy svm extapic cr8_legacy abm sse4a misalignsse 3dnowpr
efetch osvw ibs skinit wdt nodeid_msr
bogomips : 4389.19
TLB size : 1024 4K pages
clflush size : 64
cache_alignment : 64
address sizes : 48 bits physical, 48 bits virtual
power management: ts ttp tm stc 100mhzsteps hwpstate
```

```
processor : 0 - 7
cpu : POWER7 (architected), altivec supported
clock : 4004.000000MHz
revision : 2.2 (pvr 003f 0202)
timebase : 512000000
platform : pSeries
model : IBM,9119-FHB
machine : CHRP IBM,9119-FHB
```

```
model name      : ARMv7 Processor rev 0 (v7l)
processor       : 0-1
BogoMIPS        : 1993.93
Features        : swp half thumb fastmult edsp tls
CPU implementer : 0x41
CPU architecture: 7
CPU variant     : 0x3
CPU part        : 0xc09
CPU revision    : 0

Hardware        : Northstar Prototype(xiaomi miwif, bcm4709)
Revision        : 0000
Serial          : 0000000000000000
```

```
processor       : 3
model name      : ARMv7 Processor rev 5 (v7l)
BogoMIPS        : 57.60
Features        : half thumb fastmult vfp edsp neon vfpv3 tls vfpv4 idiva idivt vfpd32 lpae evtstrm
CPU implementer : 0x41
CPU architecture: 7
CPU variant     : 0x0
CPU part        : 0xc07
CPU revision    : 5

Hardware        : BCM2709 (raspberry pi 2)
Revision        : a01041
Serial          : 00000000f3919f9b
```

```
Processor       : ARMv6-compatible processor rev 7 (v6l)
BogoMIPS        : 697.95
Features        : swp half thumb fastmult vfp edsp java tls
CPU implementer : 0x41
CPU architecture: 7
CPU variant     : 0x0
CPU part        : 0xb76
CPU revision    : 7

Hardware        : BCM2708 (raspberry pi)
Revision        : 0003
Serial          : 00000000f90cbb6b
```

```
processor       : 0-3
model name      : ARMv7 Processor rev 10 (v7l)
BogoMIPS        : 790.52
Features        : swp half thumb fastmult vfp edsp neon vfpv3 tls
CPU implementer : 0x41
CPU architecture: 7
CPU variant     : 0x2
CPU part        : 0xc09
CPU revision    : 10

Hardware        : Freescale i.MX6 Quad/DualLite (Device Tree)
Revision        : 0000
Serial          : 0000000000000000
```

```
Beaglebone Black
processor       : 0
model name      : ARMv7 Processor rev 2 (v7l)
Features        : swp half thumb fastmult vfp edsp thumbee neon vfpv3 tls vfpd32
CPU implementer : 0x41
CPU architecture: 7
CPU variant     : 0x3
CPU part        : 0xc08
CPU revision    : 2

Hardware        : Generic AM33XX (Flattened Device Tree)
Revision        : 0000
Serial          : 0000000000000000
```

```
root@OpenWrt:~# cat /proc/cpuinfo
processor    : 0 ~ 1
model name    : ARMv7 Processor rev 1 (v7l)
BogoMIPS    : 2655.84
Features    : half thumb fastmult vfp edsp vfpv3 tls vfpd32 
CPU implementer    : 0x41
CPU architecture: 7
CPU variant    : 0x4
CPU part    : 0xc09
CPU revision    : 1

Hardware    : Marvell Armada 380/385 (Device Tree)
Revision    : 0000
Serial        : 0000000000000000
```

```
processor    : 0 ~ 31
vendor_id    : GenuineIntel
cpu family    : 6
model        : 45
model name    : Intel(R) Xeon(R) CPU E5-2660 0 @ 2.20GHz
stepping    : 7
microcode    : 0x710
cpu MHz        : 1199.945
cache size    : 20480 KB
physical id    : 1
siblings    : 16
core id        : 7
cpu cores    : 8
apicid        : 47
initial apicid    : 47
fpu        : yes
fpu_exception    : yes
cpuid level    : 13
wp        : yes
flags        : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc aperfmperf eagerfpu pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2 ssse3 cx16 xtpr pdcm pcid dca sse4_1 sse4_2 x2apic popcnt tsc_deadline_timer aes xsave avx lahf_lm ida arat epb pln pts dtherm tpr_shadow vnmi flexpriority ept vpid xsaveopt
bogomips    : 4418.03
clflush size    : 64
cache_alignment    : 64
address sizes    : 46 bits physical, 48 bits virtual
power management:
```

```
ubuntu@great-aigo:~$ cat /proc/cpuinfo
processor       : 0~5
vendor_id       : AuthenticAMD
cpu family      : 21
model           : 1
model name      : AMD Opteron(tm) Processor 4230 HE
stepping        : 2
microcode       : 0x600063d
cpu MHz         : 1400.000
cache size      : 2048 KB
physical id     : 0
siblings        : 6
core id         : 0
cpu cores       : 3
apicid          : 16
initial apicid  : 0
fpu             : yes
fpu_exception   : yes
cpuid level     : 13
wp              : yes
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx mmxext fxsr_opt pdpe1gb rdtscp lm constant_tsc rep_good nopl nonstop_tsc extd_apicid aperfmperf pni pclmulqdq monitor ssse3 cx16 sse4_1 sse4_2 popcnt aes xsave avx lahf_lm cmp_legacy svm extapic cr8_legacy abm sse4a misalignsse 3dnowprefetch osvw ibs xop skinit wdt lwp fma4 nodeid_msr topoext perfctr_core perfctr_nb cpb hw_pstate vmmcall arat npt lbrv svm_lock nrip_save tsc_scale vmcb_clean flushbyasid decodeassists pausefilter pfthreshold
bugs            : fxsave_leak sysret_ss_attrs
bogomips        : 5800.38
TLB size        : 1536 4K pages
clflush size    : 64
cache_alignment : 64
address sizes   : 48 bits physical, 48 bits virtual
power management: ts ttp tm 100mhzsteps hwpstate cpb
```

```
fan@bstone:~$ cat /proc/cpuinfo
processor       : 0~3
vendor_id       : GenuineIntel
cpu family      : 6
model           : 37
model name      : Intel(R) Xeon(R) CPU           L3406  @ 2.27GHz
stepping        : 5
microcode       : 0x2
cpu MHz         : 1200.000
cache size      : 4096 KB
physical id     : 0
siblings        : 4
core id         : 0
cpu cores       : 2
apicid          : 0
initial apicid  : 0
fpu             : yes
fpu_exception   : yes
cpuid level     : 11
wp              : yes
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx rdtscp lm constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc aperfmperf pni dtes64 monitor ds_cpl vmx smx est tm2 ssse3 cx16 xtpr pdcm pcid sse4_1 sse4_2 popcnt lahf_lm tpr_shadow vnmi flexpriority ept vpid dtherm ida arat
bugs            :
bogomips        : 4533.31
clflush size    : 64
cache_alignment : 64
address sizes   : 36 bits physical, 48 bits virtual
power management:
```

```
root@OpenWrt:/bin# cat /proc/cpuinfo 
processor     : 0
vendor_id     : GenuineIntel
cpu family     : 6
model          : 26
model name     : Intel Core i7 9xx (Nehalem Class Core i7)
stepping     : 3
microcode     : 0x1
cpu MHz          : 2266.660
cache size     : 4096 KB
physical id     : 0
siblings     : 1
core id          : 0
cpu cores     : 1
apicid          : 0
initial apicid     : 0
fpu          : yes
fpu_exception     : yes
cpuid level     : 4
wp          : yes
flags          : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ss syscall nx rdtscp lm constant_tsc rep_good nopl pni vmx ssse3 cx16 pcid sse4_1 sse4_2 x2apic popcnt hypervisor lahf_lm tpr_shadow vnmi flexpriority ept vpid
bugs          :
bogomips     : 4533.32
clflush size     : 64
cache_alignment     : 64
address sizes     : 40 bits physical, 48 bits virtual
power management:

KVM on Intel(R) Xeon(R) CPU L3406
```

```
[root@eypc ~]# lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                48
On-line CPU(s) list:   0-47
Thread(s) per core:    2
Core(s) per socket:    24
Socket(s):             1
NUMA node(s):          4
Vendor ID:             AuthenticAMD
CPU family:            23
Model:                 1
Model name:            AMD EPYC 7401P 24-Core Processor
Stepping:              2
CPU MHz:               1996.203
BogoMIPS:              3992.40
Virtualization:        AMD-V
L1d cache:             32K
L1i cache:             64K
L2 cache:              512K
L3 cache:              8192K
NUMA node0 CPU(s):     0,4,8,12,16,20,24,28,32,36,40,44
NUMA node1 CPU(s):     1,5,9,13,17,21,25,29,33,37,41,45
NUMA node2 CPU(s):     2,6,10,14,18,22,26,30,34,38,42,46
NUMA node3 CPU(s):     3,7,11,15,19,23,27,31,35,39,43,47
Flags:                 fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx mmxext fxsr_opt pdpe1gb rdtscp lm constant_tsc art rep_good nopl nonstop_tsc extd_apicid amd_dcm aperfmperf eagerfpu pni pclmulqdq monitor ssse3 fma cx16 sse4_1 sse4_2 movbe popcnt aes xsave avx f16c rdrand lahf_lm cmp_legacy svm extapic cr8_legacy abm sse4a misalignsse 3dnowprefetch osvw skinit wdt tce topoext perfctr_core perfctr_nb bpext perfctr_l2 cpb hw_pstate ibpb_support avic fsgsbase bmi1 avx2 smep bmi2 rdseed adx smap clflushopt sha_ni xsaveopt xsavec xgetbv1 arat npt lbrv svm_lock nrip_save tsc_scale vmcb_clean flushbyasid decodeassists pausefilter pfthreshold overflow_recov succor smca
```

```
[fan@DESKTOP-EV5RNR2 ~]$ lscpu
Architecture:            x86_64
  CPU op-mode(s):        32-bit, 64-bit
  Address sizes:         46 bits physical, 48 bits virtual
  Byte Order:            Little Endian
CPU(s):                  20
  On-line CPU(s) list:   0-19
Vendor ID:               GenuineIntel
  Model name:            12th Gen Intel(R) Core(TM) i7-12700
    CPU family:          6
    Model:               151
    Thread(s) per core:  2
    Core(s) per socket:  12
    Socket(s):           1
    Stepping:            2
    CPU(s) scaling MHz:  17%
    CPU max MHz:         4900.0000
    CPU min MHz:         800.0000
    BogoMIPS:            4224.00
    Flags:               fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dt
                         s acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc ar
                         t arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf tsc_k
                         nown_freq pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2 ssse3 sdbg fma cx16 
                         xtpr pdcm pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx 
                         f16c rdrand lahf_lm abm 3dnowprefetch cpuid_fault invpcid_single ssbd ibrs ibpb st
                         ibp ibrs_enhanced tpr_shadow vnmi flexpriority ept vpid ept_ad fsgsbase tsc_adjust
                          bmi1 avx2 smep bmi2 erms invpcid rdseed adx smap clflushopt clwb intel_pt sha_ni 
                         xsaveopt xsavec xgetbv1 xsaves split_lock_detect avx_vnni dtherm ida arat pln pts 
                         hwp hwp_notify hwp_act_window hwp_epp hwp_pkg_req umip pku ospke waitpkg gfni vaes
                          vpclmulqdq tme rdpid movdiri movdir64b fsrm md_clear serialize pconfig arch_lbr f
                         lush_l1d arch_capabilities
Virtualization features: 
  Virtualization:        VT-x
Caches (sum of all):     
  L1d:                   512 KiB (12 instances)
  L1i:                   512 KiB (12 instances)
  L2:                    12 MiB (9 instances)
  L3:                    25 MiB (1 instance)
NUMA:                    
  NUMA node(s):          1
  NUMA node0 CPU(s):     0-19
Vulnerabilities:         
  Itlb multihit:         Not affected
  L1tf:                  Not affected
  Mds:                   Not affected
  Meltdown:              Not affected
  Spec store bypass:     Mitigation; Speculative Store Bypass disabled via prctl
  Spectre v1:            Mitigation; usercopy/swapgs barriers and __user pointer sanitization
  Spectre v2:            Mitigation; Enhanced IBRS, IBPB conditional, RSB filling
  Srbds:                 Not affected
  Tsx async abort:       Not affected
```
