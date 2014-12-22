---
title: 玩了下SproutCore
author: Fan Fan
layout: post
permalink: /2012/01/17/%e7%8e%a9%e4%ba%86%e4%b8%8bsproutcore/
dsq_thread_id:
  - 612625457
categories:
  - javascript
  - UI
---
照着[文档][1]做了个hello world，运行起来后发现这货完全是浏览器本地运行，压根儿没有服务器的请求。嗯，这下是标准的HTML5了吧。

一个静态的本地应用有什么用呢？有人也有这种[疑惑][2]：没有服务器段那如何和数据库打交道呢？如果还是要[调用Rails][3]，那表明这个框架只能算上半个。

所以把这东西当作一个C-S的框架比如Swing来看就容易明白了。或者更直接点：它就是为手机上的移动web应用准备的，这个应用可以是http的web应用，也可以是本地的js+css应用。

作为一个C-S框架，比web应用要更关注界面中组件之间的交互，事件绑定和数据绑定是要优先考虑的，而这也是SproutCore的强处。

如果用它通过REST直接连CouchDB之类的文档数据库，倒是一个好的组合。

另外由于HTML5已经足够强大，比如图形和拖放，所以完全可以用javascript来写些“本地”应用。你可能说Swing和Gtk之流已经很成熟了呀？但答案是：HTML5是标准。

所以我关心的一点是SproutCore的模版语言仿佛走上了java JSTL的老路子：

<pre>{{#collection SC.TemplateCollectionView contentBinding="Todos.todoListController"}}}
   {{view Todos.MarkDoneView}}
{{/collection}}</pre>

又是XML，极端难看啊。如果页面要微调，肿么半呢？

 [1]: http://sproutcore.com/guides/getting_started.html
 [2]: https://groups.google.com/group/sproutcore/browse_thread/thread/ff026c9954589495
 [3]: http://sproutguides-drogus.strobeapp.com/rails.html