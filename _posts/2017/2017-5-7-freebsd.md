---
layout: post
title:  "FreeBSD"
date:   2017-5-7 14:20:33
typora-root-url: ../../../blog
---

<ftp://ftp.freebsd.org/pub/FreeBSD/releases/VM-IMAGES/11.0-RELEASE/amd64/Latest/> 直接就有VM版本的，不错。 

change host name: edit the file '/etc/rc.conf', and add : hostname="new.host.name" 

enable ssh: 1) sshd_enable="YES" in the /etc/rc.conf ; 2)/etc/ssh/sshd_config PermitRootLogin yes 

root@freebsd10:~ # cat /etc/rc.conf 

ifconfig_igb0="DHCP" 

hostname="freebsd10.3" 

sshd_enable="YES" 

<https://www.unixmen.com/enable-root-login-ssh-freebsd-10/>  

pfSense 开源的路由器系统，基于FreeBSD 10.3 而不是最新的11版本。起来后会直接在wan上开放web UI，不像OpenWrt的wan是在防火墙后面的，HTTPS，所以他放开？ui基于php + nginx + jQuery 开发，这个对于嵌入式的路由器可能吃不消。所以这个走的是软路由的路子。对于嵌入式比如arm，NetBSD支持更多些，当然最多的还是Linux。 

Enable SSH via WebGUI 

Navigate to System > Advanced in the WebGUI. This will show the Admin Access tab. 

Check Enable Secure Shell 

