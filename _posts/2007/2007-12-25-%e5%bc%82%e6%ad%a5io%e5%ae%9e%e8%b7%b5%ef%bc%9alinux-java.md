---
title: '异步I/O实践：Linux &amp; Java'
author: Fan Fan
layout: post
permalink: /2007/12/25/%e5%bc%82%e6%ad%a5io%e5%ae%9e%e8%b7%b5%ef%bc%9alinux-java/
blogger_blog:
  - fkpwolf.blogspot.com
blogger_author:
  - fannoreply@blogger.com
blogger_permalink:
  - /2007/12/iolinux-java.html
categories:
  - OS
---
Linux上面的称为AIO，可以参考这篇文章：

[使用异步 I/O 大大提高应用程序的性能][1]

编译时使用“gcc a.c -lrt&#8221;。这表示链接时包含库/usr/lib/librt.XX，这个库里面可能包含了aio的库，否则会出现&#8221;[Undefined reference to aio_read][2]&#8220;的错误。

 [1]: http://www.ibm.com/developerworks/cn/linux/l-async/
 [2]: http://www.linuxquestions.org/questions/programming-9/asyncronous-io-problem-386914/