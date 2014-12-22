---
title: BeagleBone TODO List
author: Fan Fan
layout: post
permalink: /2012/05/03/beaglebone-todo-list/
dsq_thread_id:
  - 686188612
categories:
  - Uncategorized
---
1. a HelloWorld assembly code. Use ADD directive. ARM load-store.

2. Interrupt in linux. Use a pin to trigger interrupt.

3. cross tool chain. 这个是用来在PC这种host的机器上编译出ARM的二进制文件，因为在ARM机器上编译太慢了。运行*sudo apt-get install gcc-arm-linux-gnueabihf*即可（hf是hard float，硬件浮点数？），然后*arm-linux-gnueabihf-gcc hellobone.c，*和一般的没有区别，然后使用scp把文件传到arm设备上。上面这种明文指定gcc路径感觉有点hard code，不知道如果用make的话是否可以直接指定目标文件的架构。