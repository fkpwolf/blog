---
layout: post
title:  "跨平台客户端 Discord,Telegram..."
date:   2018-10-21 14:20:33
---

### Discord
背后技术不大清楚，
https://www.reddit.com/r/discordapp/comments/86logj/detailed_discord_tech_stack_explanation/ 这里也没说什么。

https://blog.discordapp.com/how-discord-resizes-150-million-images-every-day-with-go-and-c-c9e98731c65d
https://blog.discordapp.com/tagged/engineering

桌面客户端安装体验很棒，直接安装，不用选择一些选项。
https://en.wikipedia.org/wiki/Discord_(software) 还是基于 Electron？我看其桌面和web页面很多元素和动画效果都是一样的。
但是我看其 Windows 安装目录有很多二进制库比如 libEGL.dll, libGLESv2.dll
Mac 下简洁些，有：Mantle.framework, ReactiveCocoa.framework, Squirrel.framework.
可以打开 developer tool，确实是基于 Electron 无疑了。

上面游戏介绍都已经汉化，看来都知道大陆是游戏大市场啊！而且很多非游戏社区也用这个客户端。

### tdesktop
https://github.com/telegramdesktop/tdesktop
Qt 5.3.2 and 5.6.2, slightly patched https://news.ycombinator.com/item?id=14088147 Qt Mac 使用了本地的观感，所以看上去应用就是用本地的风格。
FONTCONFIG_FILE=/etc/fonts/ Telegram，否则linux上面启动要好几分钟。

### Etcher
https://github.com/resin-io/etcher

## React
react-desktop 是基于 Facebook ReactJS 的 JavaScript 库，为 OS X EI Capitan 和 Windows 10 提供 Web 原生桌面体验，支持 node-webkit 和 Electron.js，但可以在任意 JavaScript 驱动的项目使用。

https://github.com/chentsulin/electron-react-boilerplate React + Electron，试了下，开发窗口里面自带很多 react 开发插件，整体开发起来和一般 web 开发差不多。
1. 如何创建 native 的菜单？
2. 如何访问本地文件？node.js 访问？

Slack 桌面3.0迁移到BrowserView http://www.infoq.com/cn/news/2017/11/slack-browser-view-3 不大懂，这个直接用系统浏览器？ 

ATOM editor: 基于JavaScript, Node.js，运行在Chrome上面。神奇。这样能做负责的UI么？不过收益于Chrome的快速优化和web开发的快速发展带来的福利。
开发语言好像是coffescript，参考这里的自己写package http://flight-manual.atom.io/hacking-atom/sections/package-word-count/
我的atom： https://atom-china.org/t/atom/1950

http://electron.atom.io/ ATOM背后的技术
C:\Users\fkpwo_000\AppData\Local\atom\app-1.10.2\resources\app\apm 包管理的位置，里面并没有react或者angular.js，可能因为性能考虑没有用这些库。

https://github.com/docker/kitematic/blob/master/CONTRIBUTING.md docker的图形管理工具也是基于electron开发的。

### CEF - Chromium Embedded Framework
https://bitbucket.org/chromiumembedded/cef
https://bbs.deepin.org/forum.php?mod=viewthread&tid=39743 网易云音乐用的这个
```
fan@jstone:/usr/lib/netease-cloud-music$ ls -lh
total 106M
-rw-r--r-- 1 root root 342K May 11 2016 cef_100_percent.pak
-rw-r--r-- 1 root root 450K May 11 2016 cef_200_percent.pak
-rw-r--r-- 1 root root 4.0M May 11 2016 cef_extensions.pak
-rw-r--r-- 1 root root 2.4M May 11 2016 cef.pak
-rwsr-sr-x 1 root root 15K Jun 28 2016 chrome-sandbox
-rw-r--r-- 1 root root 9.8M May 11 2016 icudtl.dat
-rw-r--r-- 1 root root 73M Jun 28 2016 libcef.so
drwxr-xr-x 2 root root 4.0K Nov 2 17:42 locales
-rw-r--r-- 1 root root 430K May 11 2016 natives_blob.bin
-rwxr-xr-x 1 root root 16M Jun 28 2016 netease-cloud-music
-rw-r--r-- 1 root root 642K May 11 2016 snapshot_blob.bin
```
官方 hello world 文档有点烂，看看这个 https://github.com/acristoffers/CEF3SimpleSample
这个好像是编译好的 chrome 库，有600MB。运行起来很快。Linux下一堆问题。后来发现还不如下载官方的，里面会带一个simple的例子，按照这里的文档https://bitbucket.org/chromiumembedded/cef-project。结果我卡在 fatal error: gtk/gtk.h: No such file or directory，libgtk-dev各个版本的我都安装了啊，什么鬼。后来换成 Ninja 编译就好了。然后用 cefsimple.exe --url=http://news.163.com可以打开网站，cool！但是这个为什么不在 binary 里面包含而要自己编译呢？
想找个borderless window的例子，一般都是Windows上面的 http://www.magpcss.org/ceforum/viewtopic.php?f=6&t=13628

优点么，天然跨平台，容易布局，
这个和已有的 native web 比如 atom，有啥区别？
https://github.com/zhaojunmeng/myblog/blob/master/posts/cef/hello-cef.md 这个有提到Off Screen Rendering（OSR，离屏渲染）

屌炸天的内核来袭，史上最小chromium内核miniblink！https://zhuanlan.zhihu.com/p/22611497
"cef：优点是由于集成的chromium内核，所以对H5支持的很全，同时因为使用的人也多，各种教程、示例，资源很多。但缺点很明显，太大了。最新的cef已经夸张到了100多M，还要带一堆的文件。同时新的cef已经不支持xp了（chromium对应版本是M49）。而且由于是多进程架构，对资源的消耗也很夸张。如果只是想做个小软件，一坨文件需要带上、超大的安装包，显然不能忍受。
nwjs，或者最近大火的electron：和cef内核类似，都是chromium内核。缺点和cef一模一样。优点是由于可以使用nodejs的资源，同时又自带了各种api的绑定，所以可以用的周边资源非常丰富；而基于js的开发方案，使得前端很容易上手。所以最近N多项目都是基于nwjs或electron来实现。例如vscode，atom等等。"

https://proton-native.js.org/#/ React Native 可以写桌面应用了，不必使用 Electron 了。有人写了一个渲染器，把 RN 渲染成桌面操作系统的 Native 应用。