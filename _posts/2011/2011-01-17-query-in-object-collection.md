---
title: 在对象集合中查找
author: Fan Fan
layout: post
permalink: /2011/01/17/query-in-object-collection/
dsq_thread_id:
  - 688632724
categories:
  - Language
---
最近写代码时经常遇到在一个Collection中找某个符合条件的元素。比如我要“在这些书中找到《编程之魂》”，这个简单算法在Java中得这么写：

<pre class="brush:java">private static Book getBookByName(List&lt;Book&gt; all, String bookName){
  for ( Book b : all) {
    String bName = b.getName();
    if ( bName.equals(bookName) ){
      return b;
    }
  }
}</pre>

大段的代码会打断工作的“流”：你不得不停下刚才的想法而先完成这个任务，这势必会影响效率。.NET中有个先进的玩意LINQ，用类似SQL的语法来操作collectio而不是常见的关系数据库。有了SQL语法，当然可以生成比上面复杂多的查询条件。Java开发人员只能[羡慕][1]了：在函数式编程方面，C#走在了Java前面。

Javascript中也有同样的问题，看了下jQuery，它有个方法[jQuery.grep][2]：

<pre class="brush:javascript">var arr = [ 1, 9, 3, 8, 6, 1, 5, 9, 4, 7, 3, 8, 6, 9, 1 ];
arr = jQuery.grep(arr, function(n, i){
  return (n != 5 && i &gt; 4);
});
// return 1, 9, 4, 7, 3, 8, 6, 9, 1</pre>

闭包的优势就体现出来了。而Java没有闭包，所以得整匿名函数。[Google Guava][3]扩展了JDK引以为豪的Java Collection框架（看看Guava的其他扩展，Java老矣），其中有个方法Iterables.filter(Iterable<T> unfiltered, Predicate<? super T> predicate)，实现了跟上面类似的功能。匿名函数实现起来就比闭包麻烦了。

 [1]: http://stackoverflow.com/questions/346721/linq-for-java-tool
 [2]: http://api.jquery.com/jQuery.grep/
 [3]: http://code.google.com/p/guava-libraries/