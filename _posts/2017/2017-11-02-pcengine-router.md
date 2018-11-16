---
layout: post
title:  "PC Engines APU2 路由器"
date:   2017-11-02 14:20:33
---
### 购买
试过几台电脑 PC 改装的软路由，总觉得太庞大，只做路由太浪费。和 NAS 放在一起 all-in-one 吧，又有点鸡蛋放在一个篮子里。其实路由本来就不需要多少 CPU 性能，转发而已。淘宝上面软路由不少，但是支持 kvm 直通的不多，样子也不入法眼。于是入了下面这个：

![Image 1 of APU2](/images/2017/apu2-1.JPG)

### 硬件
国内买可以直接邮件联系，也不贵，好像七八百的样子，记住这个是包含 2GB 内存的。做工相当不错，台湾代工，原件密密麻麻，看上去就赏心悦目。外壳铝制，直接接触 CPU 散热。预留 wifi 天线口的位置有防尘塞，真是用心。
AMD GX-412TC [SOC](https://www.amd.com/en/system/files?file=2017-06/g-series-soc-product-brief.pdf)，四核四线程，TDP 6W，支持 AES-NI 加速，这个是很多 Intel J1900 之流都没有的。因为没有内置 GPU（软路由也用不着），所以发热小，专用 SOC 就是省心啊！

2 miniPCI express + mSATA，淘宝上买了个 SanDisk SSD U100 24GB，大小是足够了。无线网卡暂时没有需求，我用专门的 AP。

### 软件
软件还是上 Ubuntu + OpenWrt(kvm 直通)。这台机器没有 VGA 口，只有个串口连出来，所以安装 Ubuntu 得麻烦些，因为配置好让整个安装过程通过串口显示出来，操作方式就和一般开发板类似。KVM/IOMMU 则更麻烦，Ubuntu 和 BIOS(coreboot) 都要改，此处省略万字。。。<https://github.com/pcengines/coreboot/pull/186/commits> 最新的 coreboot 应该已经 merge patch 了。

要让 OpenWrt 用上 AES-NI 加速，得用 LEDE（合并后可能新的 OpenWrt 也支持），然后`cryptsetup benchmark`性能会大幅度提升，当然具体软件是否用了硬件加速就得额外测试了。

### 部署
和几台交换机玩叠叠乐。最上面的电信光网路由器，我只用其桥接功能。
这样占用空间少，其实不好散热，一到夏天烫的吓人。

![Image 2 of APU2](/images/2017/apu2-2.JPG)
