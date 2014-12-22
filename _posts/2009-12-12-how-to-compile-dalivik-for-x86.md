---
title: 如何编译x86平台的Dalvik
author: Fan Fan
layout: post
permalink: /2009/12/12/how-to-compile-dalivik-for-x86/
dsq_thread_id:
  - 609653915
categories:
  - Mobile
---
Android Source中默认的Dalvik编译目标是ARM平台，只能在模拟机或者真机上运行，不过如果想研究它我觉得还是在x86下方便点。这里主要参考了android-porting下的<a href="http://groups.google.com/group/android-porting/msg/29167c58b3b49051" target="_blank">这篇帖子</a>，按照步骤来问题不大。（后来发现其来源于源代码目录下的docs/hello-world.html）

#### 0. 如果使用Ubunut Karmic的话，把gcc版本换成4.3的。

`sudo apt-get install gcc-4.3 g++-4.3<br />
sudo ln -s /usr/bin/gcc-4.3 /usr/bin/gcc<br />
sudo ln -s /usr/bin/g++-4.3 /usr/bin/g++`  
默认的4.4 compile时要求[更严格][1]，会出现`error: invalid conversion from<br />
‘const char*’ to ‘char*’`的提示。

#### 1. 在源代码跟目录下，运行：

` . build/envsetup.sh<br />
lunch 2 `

这主要设置编译的目标平台。

#### 2. 编译相对应的模块：

`make dalvikvm core ext framework android.policy services`

`dalvikvm`后面的几个模块是VM本身需要的一些library，比如ext.jar。如果make所有模块也可以，不过耗时就长多了，而且出现编译错误的可能性就大多了，对于我这种只懂Java的人来说handle起来很困难&#8230; 另外，使用make modules可以得到一所有模块的列表。（使用repo sync project1 project2 &#8230;可以只check out某些特定的project）

剩下的就是运行一个hello world的java类了，可以参考前面提到的帖子，相对容易。

 [1]: http://code.google.com/p/android/issues/detail?id=4347