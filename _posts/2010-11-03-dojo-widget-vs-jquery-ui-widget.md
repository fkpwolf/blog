---
title: Dijit和jQuery UI Widget对比
author: Fan Fan
layout: post
permalink: /2010/11/03/dojo-widget-vs-jquery-ui-widget/
dsq_thread_id:
  - 605422835
categories:
  - javascript
---
一直以为Dojo的Widget模型虽然先进，但是过于庞大，很难说是“非侵入式”。比如widget的声明：

<div id="_mcePaste">
  <button dojoType=&#8221;dijit.form.Button&#8221; id=&#8221;searchButton&#8221;>
</div>

<div id="_mcePaste">
  Search
</div>

<div id="_mcePaste">
  </button>
</div>

这里的dojoType就是非HTML标准的，难免让人反感。Dojo是模块化的，一个widget由template和js组成，分别为view和controller（一般json为model）。与此不同的是jQuery UI Widget采用了简单的方法：view和controller都可以嵌入到HTML主页面，view是标准的HTML，然后js会对这个element做后期处理，比如绑定事件、处理CSS和定义接口。

具体参考[这里][1]的一篇文章。这是篇信息量很大文章，值得一读。比如$(&#8216;.target&#8217;).green3()是在target element上面创建widget，感觉是在普通的DOM对象上面附上一个定制的JavaScript对象，然后$(&#8216;.target&#8217;).green3(&#8216;darker&#8217;)是调用JS对象上的darker方法。

可以看到，jQuery的做法更贴切toolkit的名字：只是一个工具，不对已有模式做大变化，相比DOJO就可以称的上一个框架了。（jQuery倾向于函数式编程，而Dojo则走的是OOP的路子。）我一直顾虑Dojo的效率，考虑到复杂的依赖关系，冗长的加载时间，如果没有对其进行定制或者对其不了解，很难做到轻量级的开发模型。（定制是必须的，Dojo也在这上面花了不少功夫）

**如果Dojo或Dijit能分离出一个类似Ext core的东西就好了！只保留一个紧凑的组件模型。**可能已经有，而我还不知道。（有个dojo mobile的东西，好像有点参考价值。）

<span style="font-size: 13.3333px;">我想象中的一种开发场景为：页面得到json reponse，通过template将其转化为html，然后使用一种绑定机制对该HTML段落进行绑定。能否有一种方式简化这个过程？这个过程需要有一致性，简洁、明了。。。</span>

 [1]: http://bililite.com/blog/understanding-jquery-ui-widgets-a-tutorial/