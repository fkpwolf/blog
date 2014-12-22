---
title: 'Promise, Future, Dojo&#8230;'
author: Fan Fan
layout: post
permalink: /2011/07/25/promise-future-dojo/
dsq_thread_id:
  - 606895498
categories:
  - Language
---
Dojo上一个普通的Ajax调用：

<pre>var xhrHandler = dojo.xhrGet({url:this.url, 
  handleAs:"json-comment-optional", content:serverQuery});
xhrHandler.addCallback(dojo.hitch(this, function(data){
  this._xhrFetchHandler(data, request, fetchHandler, errorHandler);
}));
xhrHandler.addErrback(function(error){
  errorHandler(error, request);
});</pre>

addCallback时，如果deferred已经完成，则传入的function被立刻执行。所以如果上面的xhrGet很快执行完成，则加入的callback也会被执行（这个时候消息队列为空），如同代码顺序执行。如果xhrGet较长时间完成，则加入的callback会有异步运行的效果。

上面的代码感觉很奇特，有种时空的错觉。如果dojo.xhrGet(..).addCallback(&#8230;).addCallback(&#8230;)则好点。

这个东西叫Promise，在CommonJS有单独的列出来，写法可以这样requestData(&#8220;foo.bar.com&#8221;).then(&#8230;).then()，看上去直观了很多。新的Dojo.js语法里面也有这个东西，不算新，只是我对Promise**这个词**很感兴趣，还有Future。

当命名一个抽象概念时，名字很重要啊！

后来搜了下，原来Future在java中也有这个[概念][1]，可以参考这篇[中文的文章][2]（把callback和actor鄙视了一通），大体上讲就是把一个异步的程序转为顺序的写法：

<pre>ExecutorService executor = ...//copy from jdk doc
   ArchiveSearcher searcher = ...
   void showSearch(final String target)
       throws InterruptedException {
     Future&lt;String&gt; future
       = executor.submit(new Callable&lt;String&gt;() {
         public String call() {
             return searcher.search(target);
         }});
     displayOtherThings(); // do other things while searching
     try {
       displayText(future.get()); // use future 这个地方会被block
     } catch (ExecutionException ex) { cleanup(); return; }</pre>

如果会被block的话，还有啥意义呢？不过代码的逻辑清楚很多。

正式的解释在wikipeida上面有：http://en.wikipedia.opreiki/Futures\_and\_promises Futures and promises.

看[这篇][3]dojo tutorial，你发现原来dojo.then其实对应上面的future，也就是一个包含返回数据的对象（js不需要用*get*方法），dojo.when的用处就是使普通的数据和“将来”的数据统一使用。Wikipedia对java需要get的方式为显式的future，而dojo的为隐式的。

在Actor模式中也会用到future，具体参考Akka的[这篇][4]文档。（Akka的文档比Scala完善很多，而且有java的版本，上手很容易。上面说Future is Monad，晕啊）

<pre>Future[Object] future = actorRef.sendRequestReplyFuture[Object](msg);
Object result = future.get(); //Block until result is available, usually bad practice</pre>

actor之间可以用message来访问，上面的方式方便了非actor访问actor。如果系统明确的划分成几个Actor，则future使用的地方很少。

 [1]: http://download.oracle.com/javase/6/docs/api/java/util/concurrent/Future.html
 [2]: http://www.cnblogs.com/uptownBoy/articles/1772483.html
 [3]: http://www.sitepen.com/blog/2010/05/03/robust-promises-with-dojo-deferred-1-5/
 [4]: http://akka.io/docs/akka/1.1/java/futures.html