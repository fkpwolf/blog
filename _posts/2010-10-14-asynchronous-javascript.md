---
title: Asynchronous Javascript
author: Fan Fan
layout: post
permalink: /2010/10/14/asynchronous-javascript/
dsq_thread_id:
  - 613343398
categories:
  - javascript
  - Language
  - Web Development
---
本来以为Javascript是有异步机制的，后来发现其实大多数的实现都是使用setTimeout + callback，单单使用callback是不能达到异步的，比如：

<pre lang="javascript">// callback style:
	function renderLotsOfData(data, callback){
		var success = false
		try{
			for(var x in data){
				renderDataitem(data[x]);
			}
			success = true;
		}catch(e){ }
		if(callback){
			callback(success);
		}
	}
	// using callback style
	renderLotsOfData(someDataObj, function(success){
		// handles success or failure
		if(!success){
			promptUserToRecover();
		}
	});
	renderOthers();</pre>

renderOthers必须等到callback函数运行完后再触发，所以其实这里是block的，只是callback的使用有“代码段”上的异步的感觉。

[dojo.Defferred][1]乍看很神秘，其实只不过是对callback函数的管理，可以有callback函数链的效果，也使复杂的情况下的编码更容易理解。

有些[地方][2]使用Node.js的方法[process.nextTick][3]来实现异步，文档里面简单的说“This is not a simple alias to setTimeout(fn, 0), it&#8217;s much more efficient.” 具体能做到多高效不得而知。而且Node.js虽然很cool，也只能在server端使用。所以感觉js中真正的异步也只有AJAX了，当然，大多数的异步要求也围绕request/response。

最好的方案大概是使用event的subscribe/publish模式了，这在Dojo和Node.js中都大量使用。要在Java中实现这种“异步的Observer模式”需要使用Executor的线程技术，不过由于缺少闭包，这时候模式的使用看上去更像为了弥补语言的缺陷。

Node.js号称任何东西都是异步的，比如I/O的操作，并以此获得Scalability。很多人对此表示怀疑，争论最后变为event和thread这两种模式的比较。[Node and Scaling in the Small vs Scaling in the Large][4]这篇指出scaling在大型系统上是综合的考虑，没有哪一种技术是“银弹”，或者说技术的选择已经无关紧要，架构或者设计才是重要的。当然这是冷静的思考，不过Node.js现在版本才0.48，大概没有人会用在大型系统中吧。我觉得event模式优点在于逻辑清晰，而thread代码复杂，难以维护。

<span style="text-decoration: line-through;">感觉语法级别的异步和并发也只有java能做到了，也是久经考验的。Node.js的稳定性则依赖chrome V8引擎的c++代码，多少有点不靠谱，</span>但是javascript语法灵活，这是极大优点<span style="text-decoration: line-through;">。Ruby如果要使用异步还的靠第三方包，不过一般的web应用对这方面也不需要，php啥都没有，照样十分流行。</span>

 [1]: http://www.dojotoolkit.org/api/dojo/Deferred.html
 [2]: http://caolanmcmahon.com/async_in_node_js.html
 [3]: http://nodejs.org/api.html#process-nexttick-76
 [4]: http://al3x.net/2010/07/27/node.html