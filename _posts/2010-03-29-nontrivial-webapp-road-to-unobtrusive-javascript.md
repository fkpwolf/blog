---
title: 复杂web应用的非侵入Javascript之路
author: Fan Fan
layout: post
permalink: /2010/03/29/nontrivial-webapp-road-to-unobtrusive-javascript/
aktt_notify_twitter:
  - no
dsq_thread_id:
  - 616563364
categories:
  - Web Development
---
就跟Javascript是世界上被[误解最深][1]的语言一样，HTML也是一种古老的原本简单的技术，现在却被大量甚至过度使用，旧有技术并没有考虑到方方面面，也没有鼓励和抑制某种用法。这样对于复杂应用，旧有的编程模型就得大量的改进。

### 1. 混合

早期的页面都是逻辑和展示混合在一起的。比如<button id=&#8217;sub&#8217; onclick=&#8221;submitData();&#8221;/>。

### 2.分开

采用事件绑定。比如在jquery中，*$(&#8216;#sub&#8217;).bind(&#8216;click&#8217;, function(){&#8230;})*，这样逻辑和显示分开了。这些绑定的代码大多写在页面的*onload*里面，但这不代表所有的情况。

### 3.javascript模块化

对于web2.0或者Ajax使用很多的应用，用户感觉他始终在一个页面操作，比如Gmail，左侧的栏是一直在的，不会出现整个页面刷新的情况，多数时候是页面上的某一小块更新内容。

<img class="alignnone size-full wp-image-706" title="web-dev" src="http://fkpwolf.net/WordPress/wp-content/uploads/2010/03/web-dev.png" alt="web-dev" width="409" height="378" />

比如上面页面右侧区域的内容是根据用户左侧点击操作而动态更新的，这块区域的事件绑定就需要动态的，而不能在整个页面装载时绑定。这样页面各个区域以及整个页面的javascript其实是可以分离开的。

这种web页面结构已经有点走向传统C/S界面开发了，但是html+javascript开发起来难度就大很多。为了避免走向过度复杂，当程序走到这一步需要**打住**：1）保持简单，避免过度设计（特别是对于小的应用）。只有很少的应用需要做到Gmail的复杂程度。简单是web的本质。2) 重构页面，重构操作模式，不能把C/S的开发思路硬搬到web开发。3）使用RIA之类的胖客户。

模块化为减少全局变量的使用铺平了道路。全局变量不仅指*<script>title = &#8216;All';</script>*这种声明的变量，通过*document.getElementbyId*也是对全局变量document的访问。这种方式很容易的可以访问页面其他的元素，鼓励了页面元素间的依赖关系。这种细粒度的依赖织成复杂的关系网，当测试复杂程序时会非常困难。

减少全局变量则使另外一个问题突出来：模块间的交互。在有全局变量时候，模块间可以使用这种共享变量来进行通信，现在得另外找道路。event和Observer模式是一个降低模块间耦合度的好方法，jQuery和dojo对这方面都有关注，javascript的函数式语法处理这些得心应手。有些模式比如Front controller也可以达到这种效果。

### 3.组件

当web应用不可避免的走向B/S模式时，B/S中的组件模型也被复制到html中。典型代表是Dojo，也即Digit widget，还有Ext JS。页面被划分的更细，组件有独立的javascript（事件处理），css。显然这种方式是web开发的理想方式：封装更好，重用性更好。

不过web的优点在于简单和标准，组件对已有编程模型改变太大（侵入式），是一个“框架”而不是“工具”，适合建立复杂的web应用，比如企业应用。一般的应用采用jQuery这种辅助性js库开发更轻量级，更灵活。

 [1]: http://shiningray.cn/javascript-the-worlds-most-misunderstood-programming-language.html