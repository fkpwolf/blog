---
layout: post
title:  "家（shan）酿（zhai）云计算平台"
date:   2014-12-30 14:40:00
---

##硬件
主板：华擎B85M-ITX，CPU：E3 1230 v3，内存：8G。
主板自带AR8171网卡，应该不太弱，可惜在Linux下似乎是太新的原因，WOL支持的不大好。
这板子价格399￥，后来还发现支持vt-d，实在物美价廉，当然也缺少Aspeed这种专业的远程管理的IPMI功能。至于能用多久。。。坏了就仍吧。
后来一步到位买了块Intel i350 4口千兆网卡，这个在虚拟机环境下支持的很好。taoba价大概在400$。

[<img src="http://fkpwolf.net/images/2015/i350.jpg" width="300px"/>][1]

如果硬盘休眠，整机待机功耗20W，很低了，所以我后来也就没有折腾WOL了，24h待机。

##主机（hypervisor）
Ubuntu Server。这个就是了，默认KVM也开启了。这个比起Xen、vSphere是山寨，可是我只会简单的。

##安装
QEMU命令行不会（太长了），直接virt manager搞起。安装系统比真机安装还简单，不行就格式化重来。
在主板UEFI开启了vt-d之后，可以使用PCI-E穿透，就可以直接把PCI-E网卡分配给虚拟机。如果还是不行，则要传递"intel_iommu=on"参数给内核。
通过这种方式，guest系统可以直接得到物理网卡的mac地址。Windows 8还得安装intel i350的驱动，Linux直接可用。
但是一个PCI-E插槽怎么分给多个guest系统呢？
有个东西叫SR-IOV（[支持列表](http://www.intel.com/support/cn/network/adapter/pro100/sb/cs-031492.htm)），可以把一个物理PCI-E网卡“模拟”成多个逻辑上的PCI-E设备，比如我的4口的就模拟成了4个PCI-E设备，这样分配就很直接了。

    $lspci
    01:00.0 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
    01:00.1 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
    01:00.2 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
    01:00.3 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
    03:00.0 Ethernet controller: Qualcomm Atheros QCA8171 Gigabit Ethernet (rev 10)

壮哉我淘宝山寨货，不比intel原装差啊！

##访问
因为用了PCI-E穿透的网卡，访问直接通过DHCP得到的IP即可。图形界面默认用Spice，这个和VNC类似，但是Cool点，因为没用过。
这个Spice和IPMI有点像，比如可以用键盘操作GRUB。VNC好像是操作系统的一个服务，也就是说必须完全进入到系统后才能连接。
Spice有多种平台客户端，支持比较好的只有Windows上面的Remote View，其他的比如OS X和Android都很粗糙。
连接guest的时候注意ip要填**host**的，然后用端口号来区分不同的guest。这样来guest就没有必要有外网（host网段）的ip，管理更灵活。

virt manager很方便，但是这个图形工具现在还只能在Linux上面运行，这时候可以ssh到host上面后命令行来管理。
比如`virsh list --all`列出所有vm，`virsh start opensuse-vm`会启动这个vm。

[1]:http://fkpwolf.net/images/2015/i350.jpg