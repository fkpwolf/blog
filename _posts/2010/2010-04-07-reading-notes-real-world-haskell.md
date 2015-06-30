---
title: 《Real World Haskell》读书笔记
author: Fan Fan
layout: post
permalink: /2010/04/07/reading-notes-real-world-haskell/
aktt_notify_twitter:
  - no
dsq_thread_id:
  - 697608267
categories:
  - Language
---
这本书还是不错的，举了很多例子，学起来容易多了。

1). **Lambda**。书上讲的比较少，其实有个例子就很清楚。show me the code:

Input: map (\x -> 3*x + x/4) [1,2,3,4]

Output: [3.25,6.5,9.75,13.0]

Input: foldr1 (\x y -> 2*x + y) [1,2,3,4]

Output: 16

2. **Multiple Return Value**。指一个函数可以返回多个值。Haskell中使用Tuple，大小为2的tuple为pair，这比创建一个这些值的容器类更灵活，语法上更直观，比如result, ok = add(11, 22)，这里ok可以用来指示程序运行是否有异常，在Go语言这种语法用来[弥补C的不足][1]。Haskell因为是静态类型的，所以这些Tuple都是有类型约束的；Go中则还可以对参数命名，这样函数的定义更加规范。

3. **Pattern Matching**. Twitter.

 [1]: http://www.infoq.com/cn/articles/google-go-primer