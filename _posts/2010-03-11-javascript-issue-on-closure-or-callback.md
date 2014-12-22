---
title: Javascript闭包（回调函数）的问题
author: Fan Fan
layout: post
permalink: /2010/03/11/javascript-issue-on-closure-or-callback/
aktt_notify_twitter:
  - no
dsq_thread_id:
  - 608943444
categories:
  - javascript
---
[回调函数][1]网络上一大堆，这里举个实际的例子：

<pre class="brush:html">&lt;script src="../javascripts/jquery-1.4.2.js"&gt;&lt;/script&gt;

&lt;script language="javascript"&gt;

  $(document).ready(init);

  function init(){
    console.log("....init");
    var sd = new Date();
    $('.list').each( function(){
      this.onclick= function(){
        console.log("you click,haha");
        process(sd);
      };
    });
  };

  function process(youDate){
    console.log("I got it:" + youDate);
  }

&lt;/script&gt;

&lt;div id="nav"&gt;
  &lt;div&gt;11&lt;/div&gt;
  &lt;div&gt;22&lt;/div&gt;
  &lt;div&gt;33&lt;/div&gt;
&lt;/div&gt;</pre>

逻辑很简单：当页面加载时注册回调函数，当点击div时激发回调函数。这里注册和激发是异步的。

注意回调函数可以访问上一级的成员变量sd（或许在FP中称为运行上下文），这个跟java的observer模式中的内部类可以访问容器类的this指针一样，这就是说当你点击div时，原来已经产生的时间变量sd还在，这样打印出来的时间都是旧的时间。**这是closure的灵活强大的地方**（《JavaScript: The Good Parts》里面说，“使用闭包来进行信息掩藏的方式，它是另一个减少有效全局污染的方法”。真知灼见啊！，否则又要使用大量的全局变量），但是[跟Java一样][2]，这里由于引用的关系可能会出现垃圾回收的困难，java用弱引用来处理这个问题。这篇文章[Memory leak patterns in JavaScript][3]有描述，里面提到了circular references，大概意思是javascript对象引用了DOM对象，而DOM对象反过来又引用了javascript对象。上面代码改成：

<pre class="brush:html">$('.list').click( function(){
  console.log("you click,haha");
  process(s);
});</pre>

可能更好点，因为this.onclick处**可能**隐含了DOM对象到Javascript对象的引用，而在$(&#8216;.list&#8217;)处又有javascript obj到dom obj的引用。这个我没有求证，毕竟这种内存泄漏在浏览器上关掉当前页后就应该不存在了，不会引起很明显的问题，知道有这个事就好了。

 [1]: http://www.jibbering.com/faq/faq_notes/closures.html
 [2]: http:///2009/12/23/%e5%9c%a8%e8%a7%82%e5%af%9f%e8%80%85%e6%a8%a1%e5%bc%8f%e4%b8%ad%e4%bd%bf%e7%94%a8%e5%bc%b1%e5%bc%95%e7%94%a8/
 [3]: http://www.ibm.com/developerworks/web/library/wa-memleak/