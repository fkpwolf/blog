---
title: hotcocoa上手
author: Fan Fan
layout: post
permalink: /2011/04/13/hotcocoa-hello-world/
dsq_thread_id:
  - 658451785
categories:
  - Rails
  - UI
---
1. 这是什么？

这是macruby的一个Gem，用来快速开发mac GUI应用。

2. 安装

首先安装macruby，我装的是0.11版本。然后运行`sudo macgem install hotcocoa`，这个package默认是没有安装的。

3. 运行命令`hotcocoa w3`，具体参考官方的[指南][1]。这里会产生一个和Rails类似的目录。

4.build。 直接编译的话会有问题：`Undefined symbols for architecture i386`，产生的代码都不能用，很无语。修改gem，在这里`/Library/Frameworks/MacRuby.framework/Versions/0.11/usr/lib/ruby/Gems/1.9.2/gems/hotcocoa-0.5.1/lib/hotcocoa/application_builder.rb`里面的 `archs = RUBY_ARCH.include?('ppc') ? '-arch ppc' : '-arch x86_64'`，把`-arch i386`去掉，现在的macruby都已经只有64bit的了。

5. 编译没问题的话就可以看到界面了。不过还没完，试试ruby的closure语法，不然妄走一趟。

<pre class="brush:ruby">def start
  application :name =&gt; "W3" do |app|
  app.delegate = self
  window :frame =&gt; [100, 100, 500, 500], :title =&gt; "W3" do |win|
    title = label(:text =&gt; "Hello from HotCocoa.", :layout =&gt; {:start =&gt; false})
    win &lt;&lt; title
    name = text_field(:text =&gt; 'you name please')
    win &lt;&lt; name
    win &lt;&lt; button(:title =&gt; 'go') do |b|
       b.on_action do
          title.text = "hi," + name.stringValue
       end
    end
    win.will_close { exit }
end</pre>

[<img class="alignnone size-full wp-image-963" title="macruby-hotcocoa-hell" src="http://fkpwolf.net/WordPress/wp-content/uploads/2011/04/macruby-hotcocoa-hell.png" alt="" width="444" height="154" />][2]

[][2]可以看到，比起objective-c来直观多了，想想看objective-c是怎么操作array的，痛苦。而且这里用closure的方法比用Interface Builder来建立button的事件响应要直观、简单的多，不过就要自己手工组织layout了。能否用HTML中的event bind呢？比如这样dojo.connect(&#8216;buttonId&#8217;, &#8220;onclick&#8221;, function(){&#8230;}

这里如果把name.stringValue写成name.text，**运行时**会有异常：in \`': undefined method \`value&#8217; for you name please:NSTextField (NoMethodError)，可以看到这里是直接访问cocoa的foundation的。macruby是基于cocoa的运行库的，比如NSString，但是当运行macrake时有个build的过程，时间有点长，难道macruby直接把ruby代码编译成二进制？打开打包好的app文件（这里为W3.app），

[<img class="alignnone size-full wp-image-959" title="content-structure" src="http://fkpwolf.net/WordPress/wp-content/uploads/2011/04/content-structure.png" alt="" width="184" height="228" />][3]

可以看到这里即有二进制，也有rb的源代码。可能这里的二进制文件是编译出来的，所以需要一段时间，二进制文件大概作为程序运行的入口，比如运行ruby加载rb文件，这个和有些exe结尾的可执行java程序相像。只是这样打包后coding-build-test的周期就长了，还是“刷新浏览器”的方式爽啊，当然有firebug这种工具就更爽了。

 [1]: http://www.macruby.org/hotcocoa/getting_started.html
 [2]: http://fkpwolf.net/WordPress/wp-content/uploads/2011/04/macruby-hotcocoa-hell.png
 [3]: http://fkpwolf.net/WordPress/wp-content/uploads/2011/04/content-structure.png