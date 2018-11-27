---
layout: post
title:  "Mac Tips"
date:   2015-8-6 16:27:00
---
```
to get cpu info, run "sysctl -n machdep.cpu.brand_string"
list a dependency of a binary, “otool -L file"

open dir by Finder: “open ."

color pick https://bjango.com/mac/skalacolor/ it will embed a new tab into Standard Color picker which used at app like Preview.

Nginx Mac: “brew install nginx” See http://learnaholic.me/2012/10/10/installing-nginx-in-mac-os-x-mountain-lion/ https://segmentfault.com/a/1190000002963355 
==> Pouring nginx-1.8.0.yosemite.bottle.1.tar.gz
Error: The `brew link` step did not complete successfully
The formula built, but is not symlinked into /usr/local
Could not symlink share/man/man8/nginx.8
/usr/local/share/man/man8 is not writable.r

You can try again using:
  brew link nginx
==> Caveats
Docroot is: /usr/local/var/www ( or /usr/local/Cellar/nginx/1.8.0/html)

The default port has been set in /usr/local/etc/nginx/nginx.conf to 8080 so that
nginx can run without sudo.

nginx will load all files in /usr/local/etc/nginx/servers/.

To have launchd start nginx at login:
  ln -sfv /usr/local/opt/nginx/*.plist ~/Library/LaunchAgents
Then to load nginx now:
  launchctl load ~/Library/LaunchAgents/homebrew.mxcl.nginx.plist
Or, if you don't want/need launchctl, you can just run:
  nginx
==> Summary
🍺  /usr/local/Cellar/nginx/1.8.0: 7 files, 964K
default root dir is: /usr/local/opt/nginx/html/ or /usr/local/var/www/ indeed.

brew update: update itself
brew upgrade: update installed package
brew edit virt-manager: change the formulae
Temporary workaround: Use the --no-fork switch.

Make hidden file visible:  "chflags no hidden ./private"

Enable/disable network "networksetup -setnetworkserviceenabled Ethernet on"
#设置Wi-Fi静态ip  
sudo networksetup -setmanual “Ethernet" 192.168.9.14 255.255.252.0 192.168.8.1 
#列出本机所有网络服务  
networksetup -listallnetworkservices 
#!/bin/bash
networksetup -setnetworkserviceenabled Ethernet off
sleep 2
networksetup -setnetworkserviceenabled Ethernet on

Download tool: Folx
Unzip tool: The Unarchiver

使用Command+Shift+3，可将当前的屏幕截图并以png格式保存到桌面。Command+Shift+Control+3则是将当前屏幕截图保存在剪贴板，用户可直接用Command+V粘贴在需要的地方。Command+Shift+4，按下后，光标变为十字，用户可以拖动一个矩形区域，然后区域内的部分将被截图并保存至桌面。Command+Shift+Control+4的功能与前者类似，不过截取部分保存在剪贴板。此外，还有Command+Shift+4+空格键，可以截取某一窗口或是Dock等，只要按下后在相应位置点击即可将内容保存至桌面。若想存至剪贴板，则按Command+Shift+Control+4+空格键即可。
Open folder for a given path in Mac OS X: in the Go menu, choose Go to Folder… option, Press ⇧ + ⌘ + G

https://en.wikipedia.org/wiki/MacPorts https://en.wikipedia.org/wiki/Homebrew_(package_management_software) 

东芝乞丐版蓝牙 http://superuser.com/questions/230121/how-can-i-modify-button-behavior-for-pc-only-mice-on-mac-that-dont-emit-usb-sta https://pqrs.org/osx/karabiner/xml.html.en#syntax-__KeyToKey__ 注意里面的F4不能用，要用KeyCode::EXPOSE_ALL。 新版本的 Karabiner-Elements 更好用，都不用配置。
https://github.com/tekezo/pqrs.org/blob/master/webroot/osx/karabiner/files/Karabiner-Elements-0.91.1.dmg 黑苹果上蓝牙鼠标只有这个能用，新版本很容让鼠标死掉。

Serial TTL tools : CoolTerm

$ brew install xz
$ xz -d to-extract.xz
diskutil list
diskutil unmount /dev/disk3s1
sudo dd if=RetroPieImage_ver2.3.img of=/dev/rdisk3 bs=1m
多个r能提高性能，具体看这里http://raspberrypi.stackexchange.com/a/23640

一次键盘左侧的 USB 失灵，网上一看，很多人遇到这个问题。首先 重置 Mac 上的系统管理控制器 (SMC) https://support.apple.com/zh-cn/HT201295，问题依然。然后 https://support.apple.com/zh-cn/HT204063 如何重置 Mac 上的 NVRAM，问题好了。然后，我发现重置 nvram 也会重置启动次序，原来默认是 ubuntu，现在直接启动 mac。进入 ubuntu 后再次进入 mac，键盘又坏了。什么鬼。然后只能再次重置 nvram。是 ubuntu 把系统状态搞坏了么？还是键盘兼容有问题？还是和开机前接键盘和开机后接键盘有关系？有时候重启就能解决问题。

查看所有的端口 sudo lsof -iTCP -sTCP:LISTEN -n -P

http://xclient.info/s/omnigraffle.html 图形工具

https://github.com/lhc70000/iina video player

TFTP
sudo launchctl load -F /System/Library/LaunchDaemons/tftp.plist
sudo launchctl start com.apple.tftpd
sudo chmod 777 /private/tftpboot
sudo chmod 777 /private/tftpboot/*

https://arstechnica.com/features/2018/09/macos-10-14-mojave-the-ars-technica-review/12/#h3
10.14 移除了 The subtle death of subpixel antialiasing
[开启办法](https://weibo.com/1774390367/GB7iiodFC?type=comment#_rnd1538025405140)：defaults write -g CGFontRenderingFontSmoothingDisabled -bool NO 
只需普通用户执行就可以，不要 sudo，设置需要重启。vs code 黑色背景下代码特别明显，未设置前字体很细长。
```