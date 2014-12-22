---
title: 我对CPS的理解
author: Fan Fan
layout: post
permalink: /2009/12/07/my-understanding-on-cps/
dsq_thread_id:
  - 675795124
categories:
  - Language
---
最近学习Haskell，我看的是[Yet Another Haskell Tutorial][1]，看到CPS，发现这本书写的很难懂，不知所云。后来看了wikibooks上面的[文章][2]，明白了一些。[这里][3]是一个总结，也还不错。

CPS到底是个啥？拿例子看比较容易。比较如下代码：  
`add (square x) (square y)`  
和CAS style的：  
` pythagoras'cps x y k =<br />
square'cps x $ \x'squared -><br />
square'cps y $ \y'squared -><br />
add'cps x'squared y'squared $ \sum'of'squares -><br />
k sum'of'squares`

CPS有点像倒置过来的写法，而前者则是普通的堆栈的写法。那这样CPS有什么有优点呢？

在Java中类似的写法有stringBuffer.append(&#8220;A&#8221;).append(&#8220;B&#8221;).append(&#8220;C&#8221;)，IO API中好像也有类似的方法。（在《The Productive Programmer》里面管这个叫做连贯接口 fluent interface&#8230;）

感觉有点类似管道（pipe），前面的square把结果（这里就是k）传到下一个function，这样一个个串联起来，而k起了连接的作 用。而像add( square(x), square(y))这种方式每个func相当独立(primitive)，最后有一个组合(mash-up)的过程。

而这种方式才让“从某个点停止，然后继续执行”成为可能，因为这样没有堆栈。add( square(x), square(y))这种方式会生成临时变量，会有running context。而pipe某个节点的状态就是pipe的全部，是继续“流”下去所需的所有内容，这个function是可以“重入”的。这里“流”下去就是“延续”的意思。

这种状态保存其实很多地方都有，比如http session，比如进程上下文（进程也可以sleep后再重新载入，或者恢复），但是CPS则粒度更细，可以在代码级上的重新载入，“重入”精确到某行代码。而Spring web flow这种流程级的则是精确到了flow中的某个节点，比较代码级的天书一样的代码，流程级的引入进来还是比较好的一个折中。当然这样的话，跟CPS就关系不大了。

我很早前读过[跨越边界: 延续、Web 开发和 Java 编程][4]，当时不甚理解，现在拿Haskell做例子，才明白原来这里面实现起来还是很绕的。

 [1]: http://darcs.haskell.org/yaht/yaht.pdf
 [2]: http://en.wikibooks.org/wiki/Haskell/Continuation_passing_style
 [3]: http://www.haskell.org/haskellwiki/Continuation
 [4]: http://www.ibm.com/developerworks/cn/java/j-cb03216/