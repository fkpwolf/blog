---
title: 从“在HTML中嵌入数据”到Dojo的组件模型
author: Fan Fan
layout: post
permalink: /2010/03/04/from-embed-data-in-html-to-dojo-component-mode/
aktt_notify_twitter:
  - no
dsq_thread_id:
  - 
categories:
  - Web Development
tags:
  - dojo
---
有时候web页面会包含一些隐藏数据，或者&#8221;非显示&#8221;(non-visible)数据。比如Google Reader中的博客文章列表：

<img class="alignnone size-full wp-image-662" title="table" src="http://fkpwolf.net/WordPress/wp-content/uploads/2010/03/table.PNG" alt="table" width="621" height="206" />

这里很多的Ajax操作都需要文章的唯一id，而这个id是没有显示在页面的。这种情况应该很常见。

老的方法可能是用类似<a onclick=&#8221;process(&#8216;243242&#8242;);&#8221;/>这种方法，不过现在很少用了。这把control逻辑和显示混合到一起了，元素少的时候还不明显，元素多了会有很多的process(&#8216;xxxx&#8217;)这样的重复逻辑。（我后来发现[jquery binding][1]的时候就可以传递数据，sigh，也算是一种方法吧。）

一般在HTML中嵌入数据的方法为利用HTML Element的class或者attribute。

利用class的例子为：

`<div class="entry entry-2"/><br />
<div class="entry entry-3"/>`

利用attribute的例子为：

`<div value="10" maximum="100" minimum="0"/>`

这里面maximun和minimun都是非标准的属性。

当然也可以使用hidden的element来持有数据，这些方法的缺点在于把数据嵌入到显示中来，导致页面混乱。再者这些非标准的方法使页面丧失语义性，[这里][2]详细的讨论了这个问题。HTML5中有一节[Embedding custom non-visible data][3]改进了这个问题，这样写：

`<div data-ship-id="92432"<br />
data-weapons="laser 2" data-shields="50%"<br />
data-x="30" data-y="10" data-z="90">`

个人感觉也其实也很难看，谈不上优雅，比XML差不少。或许HTML本来其实不适合做这种事情，语义网也许是解决的根本之道，唔，扯远了。

这种嵌入数据的需求在Ajax流行下越显突出。web2.0提倡的所有操作在一个页面完成，这样最后页面会很复杂，需要承载更多的数据。

如果从server端返回HTML，这些HTML数据的较之XML和json就复杂多了，因为这样的HTML是数据和显示的混合。特别是在像**表格这样的数据比较集中**的地方，HTML这种格式不适合javascript对其进行数据的后期处理。（只有在表格这样的地方，这个问题才比较突出）

使用json后，页面的rending又成了一个问题。一般可以用json templete来处理，比如[json-template][4]。这样数据是独立出来了，view也独立出来了，不足的地方是缺少一个binding的机制，否则在数据和view间的每次操作都得手工建立对应关系。

Dojo里面的dijit/widget对于这种情况给出了很好的解决方案。widget作为一个对象或者组件，数据可以直接用类似成员变量保存在对象中，不用很怪异的把它嵌入到html中，然后取值又得费劲的操作DOM。

使用组件的概念后，OOP的优点也继承到javascript中来。组件消除了全局变量，对HTML页面进行了隔离，这样规范后就不会出现原来的document.getElementById满天飞的操作。由于不是在总的document之下，一个组件的定义中就不能假设另外的组件的存在，这样，组件之间通过event来耦合，这比getElementById来直接访问是一个很大的进步。

 [1]: http://api.jquery.com/bind/
 [2]: http://www.danwebb.net/2007/10/7/custom-attributes-and-class-names
 [3]: http://dev.w3.org/html5/spec/dom.html#embedding-custom-non-visible-data
 [4]: http://json-template.googlecode.com/svn/trunk/doc/Introducing-JSON-Template.html