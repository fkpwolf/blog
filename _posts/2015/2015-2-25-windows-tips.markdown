---
layout: post
title:  "Windows Tips"
date:   2015-2-25 16:27:00
---
```

Chrome 37 UI got larger and became blurry on 125% DPI  http://superuser.com/questions/803710/chrome-37-ui-got-larger-and-became-blurry-on-125-dpi

Hiding the tool bar / showing page thumbnails by default
Go to the install directory, i.e." C:\Program Files (x86)\Adobe\Acrobat Reader DC\Reader\AcroApp\ENU". Create a new subfolder (I used "Disabled"). Move 3 files from the "ENU" folder into the new "Disabled" folder: AppCenter_R.aapp & Home.aapp & Viewer.aapp. Open a PDF and no more Tool Pane!  I originally moved just the "Viewer" file but if you clicked on "Home" or "Tools" on the toolbar you couldn't go back to the "Document." Moving all 3 files takes care of that issue. Like a lot of people I don't and won't ever use any of the tools. I just want a reader. Let me know if this works for you.

auto login - https://support.microsoft.com/zh-cn/kb/982145

Check windows version number: run "dxdiag"

MBRFix工具修复MBR很方便，打开cmd命令窗口，进入mbrfix工具所在的目录（比如c：/），然后输入命令 mbrfix /drive 0 fixmbr （注意drive 0根据您安装grub之前windows操作系统的启动分区位置而定的），再确认一下就行了。重启以后就ok了，直接可以进入Windows了。使用”高级启动“-》”启动修复“ -》命令行-》运行 Bootrec.exe
But sometimes I can't rebuildbcd(because it complain it can't find database or storage. same issue when run "bcdedit"). To fix it, run "robocopy" & "bcdedit /store" as this article.

windows 10 默认英文输入法 http://www.windows10.pro/win10-microsoft-pinyin-input-default-mode-english/

disable hi dpi for mmc.exe which can't be set in righ-menu. http://serverfault.com/questions/570785/how-can-i-make-microsoft-management-console-high-dpi-aware-in-windows-8-1

To mount ext4, install "ext2fsd"

可以使用“Shift+Space”进行半角和全角的切换

为什么 Windows 下 cmd 和 PowerShell 不能方便地自定义字体？ https://www.zhihu.com/question/36344262/answer/67191917 
现在已经更换到这里：https://github.com/be5invis/Sarasa-Gothic 

How do I find out what service is using a certain port? TCPView  https://technet.microsoft.com/en-us/sysinternals/bb897437.aspx 结合tasklist /svc看出pid对应的服务。

打开 Chrome。
开始输入您要移除的建议内容。
使用键盘上的向下箭头来突出显示相关网址。
移除这条建议内容：
Windows：按 Shift + Delete
Mac：按 Shift + fn + Delete
Chromebook：按 Alt + Shift + Backspace

工具
硬件查看工具 HWiNFO https://www.hwinfo.com/download.php 小巧精悍

4K问题
gvim https://github.com/vim/vim/issues/1059 
set guifont=等距更纱黑体_T_SC:h10

windows 10 HDR https://www.chiphell.com/thread-1865767-1-1.html 感觉要打开要各种设置啊。

https://chocolatey.org/packages/Cmder 包管理，加上一般常用命令，感觉不错。虽然没有试过，感觉里面会有一堆坑。

命令行工具推荐 https://developers.google.com/web/shows/ttt/series-2/windows-commandline，支持多 tab 窗口，不过感觉还是 putty 更简单。

```
