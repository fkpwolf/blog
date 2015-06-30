---
title: Windows下Rails的Console的彩色显示
author: Fan Fan
layout: post
permalink: /2009/11/26/windows%e4%b8%8brails%e7%9a%84console%e7%9a%84%e5%bd%a9%e8%89%b2%e6%98%be%e7%a4%ba/
dsq_thread_id:
  - 621835764
categories:
  - 工具
tags:
  - 折腾
---
很早前就发现Mac OS X下Rails控制台的日志信息可以彩色的显示，比如sql语句可以高亮，感觉很方便，这里说下如何在Windows下得到同样效果。

1）安装Cygwin，**使用setup.exe来安装Ruby**，Rails安装则照旧。

因为彩色字符是称为<a href="http://kpumuk.info/ruby-on-rails/colorizing-console-ruby-script-output/" target="_blank">ANSI escape sequences</a>的东西，windows下还不支持，所以使用Cygwin。setup.exe安装的Ruby是i386-cygwin版本的，跟我们采用一般的win32版本是不同的，ruby -v 显示为：ruby 1.8.7 (2008-08-11 patchlevel 72) [i386-cygwin]，windows下则为 ruby 1.8.6 (2007-09-24 patchlevel 111) [i386-mswin32]。 最后的日志的显示和CMD下面的是一样的：没有上色。有些<a href="http://b2.broom9.com/?p=718" target="_blank">方法</a>采用了让mswin32的ruby运行在unix终端下，比如rxvt，或者cygwin-x下（一个叫PuTTYcyg的东西更简便，神奇&#8230; [Console][1] 很强，但是没这个功能&#8230;），这样是可以彩色显示出来，但是个人实验发现console下日志显示会滞后，不会实时显示，感觉有buffer的存在（自己有开始吹毛求疵了&#8230;），看来unix终端和mswin32版本的ruby之间的兼容性不大好。

<img class="alignnone size-full wp-image-500" title="cygwin-rails" src="http://fkpwolf.net/WordPress/wp-content/uploads/2009/11/cygwin-rails1.png" alt="cygwin-rails" />

2) 安装sqlite3 driver

最难搞的地方&#8230; 最后发现可行的为：a) 用Cygwin install &#8220;gcc&#8221;, &#8220;make&#8221;, &#8220;libsqlite3-devel&#8221;, &#8220;sqlite3&#8243;;   b)gem install sqlite3-ruby, 这时会编译成本地sqlite3_api.so，没错，这里不是 sqlite3.dll，可见Cygwin下so文件是可以使用的。如果出现编译错误，可能要下载sqlite3源文件，然后再：$ gem install sqlite3-ruby-1.2.5.gem &#8212; &#8211;with-opt-include=/cygdrive/d/dev/sqlite-3.6.20/

参考的<a href="http://oldwiki.rubyonrails.org/rails/pages/RailsOnWindowsCygwinWithSQLite3" target="_blank">RailsOnWindowsCygwinWithSQLite3</a>这篇文章前后矛盾，可见Cygwin的水还是很深的&#8230;  搞了这么多，发现Windows上面开发还是蹩脚，可是有啥办法呢，使用习惯了，改成别的还真难。

<img class="alignnone size-full wp-image-498" title="cygwin-rails2" src="http://fkpwolf.net/WordPress/wp-content/uploads/2009/11/cygwin-rails2.png" alt="cygwin-rails2" />

12.20 add: 上面控制台中SQL显示是白色的，可以在Console 2中把白色映射成其他明显的颜色，但是在PuTTY中这招就不行，不解。个人是更prefer后者的，感觉功能更强大，颜色的定制可以看这里[Custom PuTTY Color Themes][2]，很喜欢里面dersert的主题，更多关于Putty的调整可以看这篇[Improving Putty settings on Windows][3]。

 [1]: http://sourceforge.net/projects/console/
 [2]: http://www.igvita.com/2008/04/14/custom-putty-color-themes/
 [3]: http://dag.wieers.com/blog/improving-putty-settings-on-windows