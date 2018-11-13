---
layout: post
title:  "行转列的 k-v 数据库"
date:   2017-07-02 14:20:33
---

leveldb 性能测试
LevelDB 还有一个特点是 —— 写性能远高于读性能 "先说随机写，它的写都是先记录到日志文件去的，在日志文件满之前只是简单的更新memtable,那么就把随机写转化成了顺序写。在日志满了后，把日志里面的数据排序写成sst表同时和之前的sst进行合并，这个动作也是顺序读和写。大家都知道传统磁盘raid的顺序读写吞吐量是很大的，100M左右是没有问题。在写日志文件的时候，用到是buffer IO，也就是说如果操作系统有足够的内存，这个读写全部由操作系统缓冲，效果非常好。即使是sync写模式，也是以数据累计到4K为一个单位写的，所以效率高。那么随机读呢？这个它解决不了。但是ssd盘最擅长随机读了。这个硬件很自然的解决了这个问题。所以leveldb的绝配是ssd盘的raid.” http://blog.yufeng.info/archives/1327

为什么选4k，因为4KB是现代SATA接口下的最小数据块规模，数字更小可能受压缩、命令流程控制的影响，而数字更大则会受连续访问的影响。那对于 pci-e ssd，可以放大4k 了？对于 SSD 来说，读的性能更能反映其性能，因为写可能是先写到缓存中。

https://github.com/google/leveldb
公司电脑，SSD KINGSTON SA400S37240G， i5 CPU
fandeMac:leveldb fan$ ./out-static/db_bench
LevelDB:    version 1.20
Keys:       16 bytes each
Values:     100 bytes each (50 bytes after compression)
Entries:    1000000
RawSize:    110.6 MB (estimated)
FileSize:   62.9 MB (estimated)
WARNING: Snappy compression is not enabled
------------------------------------------------
fillseq      :       4.430 micros/op;   25.0 MB/s     
fillsync     :      51.263 micros/op;    2.2 MB/s (1000 ops)
fillrandom   :       3.984 micros/op;   27.8 MB/s     
overwrite    :       4.235 micros/op;   26.1 MB/s     
readrandom   :       4.318 micros/op; (1000000 of 1000000 found)
readrandom   :       3.711 micros/op; (1000000 of 1000000 found)
readseq      :       0.166 micros/op;  668.0 MB/s    
readreverse  :       0.308 micros/op;  359.0 MB/s    
compact      :  693897.000 micros/op;
readrandom   :       2.339 micros/op; (1000000 of 1000000 found)
readseq      :       0.139 micros/op;  795.4 MB/s    
readreverse  :       0.246 micros/op;  450.2 MB/s    
fill100K     :    4904.830 micros/op;   19.4 MB/s (1000 ops)
crc32c       :       1.066 micros/op; 3663.8 MB/s (4K per op)
snappycomp   :    3374.000 micros/op; (snappy failure)
snappyuncomp :    3202.000 micros/op; (snappy failure)
acquireload  :      12.601 micros/op; (each op is 1000 loads)
fillsync 快了很多，官方的测试是机械硬盘，sync会写到硬盘，所以 ssd 快了很多。但是 fillseq 慢了很多啊。这个测试更多是 IO 相关吧，难道也看 CPU？fillseq 其实是写到缓存中的，所以和 CPU+内存有关系。
随机读快了，但是也不是快很多。按道理ssd 比机械硬盘要快很多啊。SSD 垃圾了点？差不多所有的 ssd 4k 随机读取速度为20 ~ 30 MB/s，这个和上面的基本吻合。但是机械硬盘一般只有0.7 MB/s啊，差不多慢了30倍。而官网 readrandom  : 16.677 micros/op; 不成比例啊。难道上面测试的随机读不是4k？

root@great-silverstone:~/leveldb# out-static/db_bench
LevelDB:    version 1.20
Date:       Fri Oct 27 17:33:05 2017
CPU:        8 * AMD Opteron(tm) Processor 4386
CPUCache:   2048 KB (L2缓存？)
Keys:       16 bytes each
Values:     100 bytes each (50 bytes after compression)
Entries:    1000000
RawSize:    110.6 MB (estimated)
FileSize:   62.9 MB (estimated)
WARNING: Snappy compression is not enabled
------------------------------------------------
fillseq      :       4.420 micros/op;   25.0 MB/s     
fillsync     :   42371.754 micros/op;    0.0 MB/s (1000 ops)
fillrandom   :      13.551 micros/op;    8.2 MB/s     
overwrite    :      20.956 micros/op;    5.3 MB/s     
readrandom   :       6.782 micros/op; (1000000 of 1000000 found)
readrandom   :       4.308 micros/op; (1000000 of 1000000 found)
readseq      :       0.224 micros/op;  492.8 MB/s    
readreverse  :       0.381 micros/op;  290.2 MB/s    
compact      : 3167857.000 micros/op;
readrandom   :       2.745 micros/op; (1000000 of 1000000 found)
readseq      :       0.188 micros/op;  587.0 MB/s    
readreverse  :       0.329 micros/op;  335.7 MB/s    
fill100K     :    6323.643 micros/op;   15.1 MB/s (1000 ops)
crc32c       :       2.097 micros/op; 1862.8 MB/s (4K per op)
snappycomp   :    4879.000 micros/op; (snappy failure)
snappyuncomp :    4490.000 micros/op; (snappy failure)
acquireload  :       0.666 micros/op; (each op is 1000 loads)