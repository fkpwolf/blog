---
title: 发布小米路由硬盘版最新DD-WRT固件
author: Fan Fan
layout: post
permalink: /2014/08/30/xiaomi-r1d-dd-wrt-port/
categories:
  - 电子制作
---
<span style="color: #444444;">基本功能都可以使用，包括双频无线和硬盘。内核版本Linux 3.10.53。</span><br style="color: #444444;" /><br style="color: #444444;" /><span style="color: #444444;">刷机大概步骤为：1）打开SSH；2）通过TFTP方式刷机。详细步骤见黄鹤兄的图文指南 </span><a style="color: #336699;" href="http://www.right.com.cn/forum/thread-143121-1-1.html" target="_blank">http://www.right.com.cn/forum/thread-143121-1-1.html</a><span style="color: #444444;"> 。一般TFTP客户端的都可以使用，官方的地址是 </span><a style="color: #336699;" href="http://tftpd32.jounin.net/" target="_blank">http://tftpd32.jounin.net/</a><span style="color: #444444;"> 。刷机有风险，建议有经验用户尝试。</span><br style="color: #444444;" /><br style="color: #444444;" /><span style="color: #444444; font-size: large;">下载地址  <a style="color: #336699;" href="http://pan.baidu.com/s/1pJ3BQn5" target="_blank">http://pan.baidu.com/s/1pJ3BQn5</a></span><br/><span style="color: #444444; font-size: large;">GitHub源代码  <a style="color: #336699;" href="https://github.com/fkpwolf/dd-wrt" target="_blank">https://github.com/fkpwolf/dd-wrt</a></span><br style="color: #444444;" /><br style="color: #444444;" /><span style="color: #444444;">1. 刷机后重启一次后在samba里面就可以看到硬盘分区。如果是从小米原生固件过来，可以看到有/mnt/sda1 &#8212; /mnt/sda4四个分区。/mnt/sda4容量最大。</span><br style="color: #444444;" /><span style="color: #444444;">2. 如果发现只有一个无线信号，设置&#8221;无线“&#8211;>”基本设置“&#8211;>“频道宽度”为“自动”即可。小米默认为20MHZ。</span>

[<img class="alignnone wp-image-1509 size-medium" src="http://fkpwolf.net/WordPress/wp-content/uploads/2014/08/miwif-show-298x300.png" alt="miwif-show" width="298" height="300" />][1]

&nbsp;

 [1]: http://fkpwolf.net/WordPress/wp-content/uploads/2014/08/miwif-show.png
