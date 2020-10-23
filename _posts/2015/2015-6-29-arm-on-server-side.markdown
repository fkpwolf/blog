---
layout: post
title:  "ARM on Server Side"
date:   2015-6-29 14:20:33
typora-root-url: ../../../blog
---

allwinner A64 <http://www.allwinnertech.com/en/clq/processora/6053.html> 

<https://www.96boards.org/> 

<http://www.jide.com/mini> Remix Mini, 4 core A53 Allwinner

<http://www.cnx-software.com/2015/04/08/rockchip-rk3368-64-bit-arm-android-boxes-are-coming-soon/> 

<http://www.virtualopensystems.com/en/solutions/guides/kvm-on-arm/> 

<http://www.linux-kvm.org/page/Processor_support#ARM:>

SoC – Rockchip RK3368 octa core Cortex A53 processor @ 1.5 GHz with PowerVR G6110 GPU

<http://www.cnx-software.com/2015/07/18/tronsmart-orion-r68-64-bit-arm-tv-box-sells-for-up-to-50-discount/> 

海美迪H7, 天敏 D8 64bit 

the (rk3368) cortex a53 is less powerful than the (rk3188) a9 or (rk3288) a17

96boards

DragonBoard 410c Development <http://www.cnx-software.com/2015/11/21/dragonboard-410c-development-board-quick-start-guide-and-android-benchmarks/>

LeMaker Hikey <http://www.cnx-software.com/2015/09/23/lemaker-hikey-96boards-development-board-comes-with-1-to-2gb-ram-sells-for-75-and-up/>

AMD 皓龙™ A1100 处理器 <http://www.amd.com/zh-cn/products/server/opteron-a-series/a1100> 

<http://beagleboard.org/x15> 

<http://www.cnbeta.com/articles/tech/670109.htm> 早期跑分曝光：ARM在服务器CPU领域崭露头角

这些商业主板对于个人来说还是太贵了吧。ARM 只是核多就厉害？Intel 的优势呢？可能 Intel I/O 吞吐量更大，而这里只是评估了 cpu 计算性能。

<https://www.cnx-software.com/2017/11/09/odroid-mc1-quad-board-cluster-launched-for-220/> 这里说 24个 ARM A17核(MIQI)比 Intel SOC 4核在编译代码时还要快，真的是这样？编译kernel时核数的多少这么重要？[Build farm](http://wiki.ant-computing.com/Choosing_a_processor_for_a_build_farm) 对比。似乎编译对频率要求不高，增加 CPU 数更为明显。 

<http://b2b.gigabyte.com/ARM-Server/H270-T70-rev-110#ov>  8 x Cavium® ThunderX™ 48-core ARM processors

![](/images/2015/arm-server-gigabyte.jpg)

<https://www.packet.net/bare-metal/servers/type-2a/> your VPS on this box, 0.5$ / hour. Packet的服务号称是bare metal的，可以完全控制机器？

Ampere SoC Designed for Cloud Computing Comes with 32 ARMv8 Cores @ 3.3 GHz, Supports up to 1TB RAM <https://www.cnx-software.com/2018/02/06/ampere-soc-designed-for-cloud-computing-comes-with-32-armv8-cores-3-3-ghz-supports-up-to-1tb-ram/> 

其实 ARM 或者 x86 核心对于上面的应用来说并不重要，Intel 也能做到很便宜的价格。可能稍微重要的地方在于：

1. 核心更多，更适合虚拟化； 
2. 功耗低 
3. 边缘计算

<https://www.cnx-software.com/2017/11/11/gateworks-newport-sbcs-powered-by-cavium-octeon-tx-64-bit-arm-soc-are-designed-for-network-applications/>

![](/images/2015/arm-server-cavium.png)

Optimizing JPEG Transformations on Qualcomm Centriq Arm Servers with NEON Instructions 

<https://www.cnx-software.com/2018/04/14/optimizing-jpeg-transformations-on-qualcomm-centriq-arm-servers-with-neon-instructions/>

使用 NEON 指令来优化图形处理，里面用了 linux 的命令 perf 来定位那个方法耗时最长。 

<https://wiki.odroid.com/odroid-xu4/application_note/software/kvm>

<https://magazine.odroid.com/article/kvm-odroid-xu4/>

Odroid 似乎确凿可以用KVM 

<https://wangxu.me/hyper/2016/10/17/hyper-arm64/index.html> 这里表面 pine64 可以运行 kvm? 还只是容器? 

![](/images/2015/arm-server-armada.jpg)

[The vSphere client screenshot](https://www.cnx-software.com/2018/08/28/vmware-esxi-arm64-hypervisor/) above shows VMWare ESXi 6.8.2 running on Marvell ARMADA 8040 based MACCHIATOBin board with four Cortex A72 cores, four virtual machines, and 184 day uptime… 

<https://www.cnx-software.com/2018/11/27/amazon-arm-server-a1-instances/>

Amazon has developed AWS Graviton processors optimized for cloud applications and delivering power, performance, and cost optimizations over their Intel counterpart. 

专门为他人打造机器 -》 新的思路

简单，便宜，容易上手

vt-d 机器：很多软路由用 i3 5005，

<https://en.wikipedia.org/wiki/Skylake_(microarchitecture)> skylake连最低端的 Celeron G3900 都支持 vt-d。这款 cpu 散片 taobao 178￥。G3900T: TDP 35W, 200￥。Pentium G4560, 470￥，支持多线程，可以比拼i3。

<https://en.wikipedia.org/wiki/List_of_Intel_codenames>

同样的，<https://en.wikipedia.org/wiki/Kaby_Lake> 也全线支持 vt-d + 4k 显示。

![](/images/2015/arm-server-100chipset.png)

<http://diy.pconline.com.cn/804/8044244.html> ，一般不会选择 h170。但是我看<http://www.asrock.com/mb/Intel/H110M-HDV/?cat=Specifications> 这个也支持 pci-x16 v3模式。<https://ark.intel.com/products/90590/Intel-H110-Chipset> 这里显示只有 v2

matx还是选择 b150，有两条 pci-x8。

<http://www.asrock.com/mb/Intel/B150M%20Pro4SD3/?cat=Specifications> 400￥可惜是 DDR3 的

<http://www.asrock.com/mb/Intel/B150M%20Pro4Hyper/> 这个是 DDR4的，可以超频，不过似乎走远了

gpu 如果显卡的钱出了，主板和 cpu 也没有必要配低端的了吧。非也，这个是灵活配置，因为这个显卡可能有其他用途。

ARM 

<http://www.cnx-software.com/2017/01/17/pine64-introduces-sopine-a64-allwinner-a64-som-and-sopine-model-a-baseboard/> 这个可以做成 SOM 的集群，还准备从网卡 PXE 启动。

[Hypriot](https://blog.hypriot.com/) 在 Raspberry Pi 上面运行容器技术，这个 OS 列在 kubeadm 的支持列表中。其 FAQ 说支持全系列树莓派，但是 aarch64 只能运行在 Raspberry Pi 3 上面吧？

<https://insights.ubuntu.com/2017/01/30/installing-a-diy-bare-metal-gpu-cluster-for-kubernetes> 这个用 MAAS 管理机器，MAAS 装在 raspberry pi 上面，用 k8s 管理集群，每台机器都是 nuc 小电脑，电源管理是手动（自己开关机？）。然后居然在上面运行 gpu + cuda。这个集群多半还是以主机为单位，多主机间的 cuda 协作代价应该很大吧。

POE <https://www.kickstarter.com/projects/pisupply/pi-poe-switch-hat-power-over-ethernet-for-raspberr> Pi PoE Switch HAT 像这样的供电设备，再加上一个 POE 百兆交换机，可以把尺寸做到很小。

<https://www.raspberrypi.org/documentation/hardware/raspberrypi/bootmodes/net_tutorial.md> NETWORK BOOT YOUR RASPBERRY PI


|                                                              | SOC                          | Mem          | Price | Etc.                                                         |
| ------------------------------------------------------------ | ---------------------------- | ------------ | ----- | ------------------------------------------------------------ |
| NanoPi Fire3, [taobao](https://item.taobao.com/item.htm?id=561209653609) | S5P6818, 8 * A53             | 1G           | 236￥ | [96 core cluster](https://www.cnx-software.com/2018/07/10/nanopi-fire3-cluster-diy-portable-solution-develop-distributed-software/) |
| Rock64, [taobao](https://item.taobao.com/item.htm?id=557302774166) | RK3328, 4 * A53              | 4G           | 364￥ |                                                              |
| ROC-RK3328                                                   | RK3328, 4 * A53              |              |       |                                                              |
| MIQI, [taobao](https://item.taobao.com/item.htm?id=536476862763) | RK3288, 4 * A17              | 2G, 16G eMMC | 479￥ |                                                              |
| Khadas VIM2, [taobao](https://item.taobao.com/item.htm?id=557820472911) | Amlogic S912, 8 * A53        | 2G, 16G eMMC | 600￥ |                                                              |
| Odroid HC1                                                   | Exynos5422, 4 * A15 + 4 * A7 | 2G           | 49$   |                                                              |
| Orange Pi [RK3399](https://www.cnx-software.com/2018/01/29/orange-pi-rk3399-development-board-launched-for-109/) | 4 * A72, 4 * A53             |              | 109$  |                                                              |

感觉这些板子只要能稳定运行 Docker 就可以。代码支持并行一则开发成本高，而则伸缩性差，不如全分布式方案。map-reduce就是分布式的，vm 的也是，现在有个更简单的，就是 Docker，而 Docker 要运行好，一则内存，一则 CPU 核数。E5 的 cpu，14 core 28 th，3000￥。odroid mc1, 32 cpu, 8G dram，just 220$. It's document support Docker Swarm, build farm & PXE boot. Mabye the only 8G memeory is a shortcome.

<https://www.cnx-software.com/2019/01/07/nanopi-neo4-build-farm-rk3399-overclocking/> 这个是 NanoPi NEO4 RK3399，CPU 颇有优势，而且内核和文档都很成熟。

机箱/外壳也是很重要的，多个主板/开发板需要放在一个盒子里面，外接一个电源。

<https://github.com/rook/rook/issues/1411> 从这里看不支持 arm docker image，arm64似乎支持但也不成熟，这个主要看ceph 上游是否支持。但是我看 ubuntu apt cache 里面有 ceph 包，只是客户端？ 

<https://www.cnx-software.com/2017/08/10/hardkernel-to-launch-stackable-49-odroid-hc1-home-cloud-200-odroid-mc1-cluster-solutions/> glusterfs 倒是很多文档 <https://magazine.odroid.com/article/exploring-software-defined-storage-glusterfs-odroid-hc1-part-1-server-setup/> 

<https://www.cnx-software.com/2020/10/21/turing-pi-2-mini-itx-cluster-board-takes-four-raspberry-pi-compute-modules-4/> 基于Raspberry Pi CM4 module，可以在小板子上面插很多 module。<https://www.pine64.org/clusterboard/> 这个也一样，都是用了交换机来连接各个小板，都是集中供电供冷。如果要搭建给Glusterfs这种分布式文件系统服务器，这些 SOM 板子必须各自包含存储接口，比如SATA，但是这种一般都很少，如果pci-e转sata则增加了成本，如果弄个pci-e nvme硬盘似乎可选的就多了。

[Banana Pi to Launch a 24-Core Arm Server](https://www.cnx-software.com/2018/12/26/banana-pi-24-core-arm-server/) 规格未知，CPU 据说是 SocioNext SC2A11。[V-Raptor](https://www.cnx-software.com/2019/01/08/v-raptor-24-core-arm-server-socionext-sc2a11/) 也是同样的 CPU，不过似乎是 PCI 接口直接查到主板上。

AWS 现在(2018.12.30)也提供 [Arm 服务器](https://aws.amazon.com/cn/blogs/china/new-ec2-instances-a1-powered-by-arm-based-aws-graviton-processors/)，使用的是 Annapurna Labs Graviton Processors，这种 SOC 个人是无缘接触了。

[PINE64 Plans to Move their Website on a 24-node RockPro64 Cluster](https://www.cnx-software.com/2019/03/29/pine64-website-rockpro64-cluster/) ECC 很重要，但是为什么路由器都用的是普通内存呢？
