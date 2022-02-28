---
layout: post
title: "各种 Database"
date: 2021-7-8
categories:
  - Database
  - BigData
typora-root-url: ../../../blog
---

企业上云系列之开源数据库的现状 http://www.infoq.com/cn/articles/situation-of-the-open-source-database
对于很多应用来说，其领域对象模型并不适合于转换成关系数据库形式来存储。这也是非关系型数据库（NoSQL）得以流行的原因。NoSQL 数据库的种类很多，包括键值对数据库、面向文档数据库和图形数据库等。

http://nosql-database.org/ 所有的 nosql 数据库

https://db-engines.com/en/ranking DB-Engines Ranking

https://use-the-index-luke.com/ Use The Index, Luke!

SQLite Is Serverless
* https://www.sqlite.org/serverless.html
* https://www.sqlite.org/whentouse.html

为什么数据库不应该使用外键 https://draveness.me/whys-the-design-database-foreign-key/
为什么 MySQL 使用 B+ 树 https://draveness.me/whys-the-design-mysql-b-plus-tree/

国内的数据库博主
* https://www.zhihu.com/people/fuyufjh/posts
* https://www.zhihu.com/column/c_1037748468491689984
* https://draveness.me

### Leveldb 和 RocksDB
Leveldb 和 RocksDB 在大 value 场景下的一些问题 http://idning.github.io/leveldb-rocksdb-on-large-value.html
获得PCC性能大赛第一名方案背后的 RocksDB 引擎:5分钟全面了解其原理 http://weibo.com/ttarticle/p/show?id=2309404088129911940161 RocksDB 是基于levedb，借鉴了hbase思想。据说为 SSD 这种存储做了优化。

[小米分布式 Key-Value 存储系统](https://www.oschina.net/p/xiaomi-pegasus) 基于 RocksDB 对比了 HBase 的缺点

[既生 Redis 何生 LevelDB ？](https://zhuanlan.zhihu.com/p/53299778)

几款主流 NoSql 数据库的对比 http://www.cnblogs.com/vajoy/p/5471308.html
leveldb 和 mongodb 很像，但是作为一个数据库后者功能更全面，前者性能是好，但只是一个库。
浅析 Bigtable 和 LevelDB 的实现 http://draveness.me/bigtable-leveldb.html 实现和hbase比较像，但是leveldb用了memtable作为磁盘table的中介和缓存，性能会提高，而hbase则偏向大数据情况下的处理。主要是 Leveldb 不支持分布式。

【Rocksdb实现及优化分析】 JoinBatchGroup http://kernelmaker.github.io/Rocksdb_Study_1 TiDB 底层也基于这个

badger https://github.com/dgraph-io/badger separating values from keys, significantly reducing the write amplification compared to a typical LSM tree.
[badger 一个高性能的LSM K/V store](https://colobu.com/2017/10/11/badger-a-performant-k-v-store/)
[如何评价 Badger (fast key-value storage)？](https://www.zhihu.com/question/59895275) "基本思路是RocksDB基于LevelDB为SSD优化，但不是为SSD尤其是目前超高随机读写能力的NVME SSD设计．"
[badger 事务过程笔记](https://zhuanlan.zhihu.com/p/395229054)

### LeanStore
a high-performance OLTP storage engine optimized for many-core CPUs and NVMe SSDs. https://dbis1.github.io/leanstore.html
Talk https://twitter.com/andy_pavlo/status/1389042478796492800

### Cassandra 
Netflix 用的比较多
分布式 Key-Value 存储系统：Cassandra 入门 https://www.ibm.com/developerworks/cn/opensource/os-cn-cassandra/
关于Cassandra的错误观点 http://www.infoq.com/cn/articles/cassandra-mythology
https://zh.wikipedia.org/wiki/Cassandra 宽列存储模型(Wide Column Stores)
ScyllaDB Cassandra优化版本，DPDK，开源。对 Linux 网络，缓存做了很多改变，是一个传统软件优化的很好例子。
https://www.zhihu.com/question/35956679
https://www.scylladb.com/2018/03/29/scylla-kubernetes-overview/ https://github.com/scylladb/scylla-code-samples/blob/master/kubernetes-scylla/README.md 优化程度这么高，运行在 k8s 上面还有优势么？

### RethinkDB
听说最近死而复生，而且号称实时 web 开发，"RethinkDB是第一个数据库使用了一种令人激动的新的数据库的访问模型，而不是轮询数据库更改，开发者可以命令RethinkDB实时的向应用连续推送更新查询结果。” http://www.infoq.com/cn/news/2017/02/RethinkDB-join-Linux 一个数据库怎么和 web 开发关系起来？试试看。
伯克利推出世界最快的KVS数据库[Anna](https://mp.weixin.qq.com/s?__biz=MzU1NDA4NjU2MA==&mid=2247492605&idx=1&sn=8e209f02a40b376f9cfa53058322243b&chksm=fbea5a32cc9dd324b9618f19f5b1bd7a0271114e01915dc5d1f54fa8389b0644a77bdc447652&scene=27#wechat_redirect)：秒杀Redis和Cassandra https://zhuanlan.zhihu.com/p/34603927
https://www.rethinkdb.com/docs/install/ubuntu/
然后 https://www.rethinkdb.com/docs/quickstart/
rethinkdb --bind all，可以 bind 所有的端口而不仅仅 localhost。
启动后会在8080端口打开一个 ui，设计的还是相当专业。
运行了几个命令，还行。不过还是没有看到后段怎么推送查询结果。
https://www.rethinkdb.com/docs/guide/javascript/ node.js 作为客户端调用，我 mac 上运行 js 。
node 作为消息驱动的开发模式和这个类型的数据库很匹配啊。怎么和前端交互？websocket？
实时推送查询结果，似乎很牛。不过应用场景比较少，二则这种性能的问题容易被转移成其他问题。
https://github.com/rethinkdb/rethinkdb 这里面有提供他的使用场景，都是和实时紧密相关。

### Riak
《Designing Data-Intensive Applications》提到较多
类 Dynamo 的分布式 Key-Value 系统，Erlang 编写，Bitcask为其存储引擎
支持 global secondary index by term
HaloDB
https://github.com/yahoo/HaloDB/blob/master/docs/WhyHaloDB.md，类似 Bitcask
解决 LSM 写放大的问题，Java 编写
"an index in memory which stores all the keys, and append-only log files on the persistent layer which stores all the data"  混合的技术

### YugabyteDB
https://github.com/yugabyte/yugabyte-db
[YugabyteDB 介绍](https://zhuanlan.zhihu.com/p/102589603)
partition https://docs.yugabyte.com/latest/architecture/docdb-sharding/ a new term 'tablet'.
master server: keep metadata & coordinate. TServer: query & storage. The arch is some like HBase.

### FoundationDB
[苹果公司开源FoundationDB的简单分析](https://cloud.tencent.com/developer/article/1164667) 『和其他NoSQL不一样的是，FoundationDB的Key-Value Store实现了强一致性，而非最终一致性』大数据时代的Key-Value Store大体上分为两类：
1. 以BigTable和HBase为代表的，分区键（Partition Key）全局排序，通常采用的是范围分区（Range Partition）
2. 以AWS DynamoDB和Cassandra为代表，分区键（Partition Key）不排序，通常采用的是哈希分区（Hash Partition）

https://apple.github.io/foundationdb/getting-started-linux.html 安装挺容易，[集群](https://apple.github.io/foundationdb/building-cluster.html)也不复杂，依赖很少，果然是苹果出的，用户体验不错。https://apple.github.io/foundationdb/class-scheduling.html 这个是实例一般的 CRUD 操作，非 sql 语法，虽然有点像 k-v 操作，但是是支持事务的。https://www.voltdb.com/blog/2015/04/01/foundationdbs-lesson-fast-key-value-store-not-enough/ Plans for SQL Layer? issue  也就是说 sql 现在还不能用，tidb 作者建议使用 tidb 的 sql layer，说是已经抽象，但是 foundationdb 已经支持 acid 了，并非一个简单的 k-v 数据库，而且 tidb 有个 pd，这个在 fdb 没有对应的吧？https://github.com/opentradesolutions/opentick 这个提供了初步的 sql layer 支持。
『在核心外， FoundationDB通过分层设计的方式，实现了对各种数据模型，比如文档数据库，图数据库，关系数据库的支持。』这个也是 https://www.foundationdb.org/ 上面说的首要特征：Multi-model data store，https://apple.github.io/foundationdb/layer-concept.html 这里解释一般选择数据库就选择了数据模型（关系、文档）和存储引擎以及访问方式（REST, SQL），而用分层的概念解耦了数据模型和存储引擎，FDB 的 k-v 存储引擎能用在各种数据模型上面，而这种灵活性（其他 k-v 数据库也有）不丢失 ACID。
其文档写的很全，不错。

### 非通用数据库
* https://db-engines.com/en/system/LokiJS%3BLovefield%3BPouchDB System Properties Comparison LokiJS vs. Lovefield vs. PouchDB JavaScript 数据库，轻量级，可以在客户端（浏览器、手机）运行，然后同步整个db data to server side. Azurite is an open source Azure Storage API compatible server (emulator) 有用到。
* https://www.arangodb.com/ 专门为图算法优化的数据库。
* TSDB

### 国内数据库
* TiDB
* openGauss 华为开源的数据库  https://opengauss.org/zh/
* ZNBase 浪潮的分布式数据库 http://www.znbase.com/
* 淘宝 [OceanBase](https://www.zhihu.com/question/19841579) 融合了 mysql 的事务并且兼容 mysql，以及用了 leveldb 类似的内存写的技术，不过虽然开源但是 commit 特别少，现在已经成为阿里云上面的一个产品了。

### 比较传统数据库
CAP理论十二年回顾："规则”变了 http://www.infoq.com/cn/articles/cap-twelve-years-later-how-the-rules-have-changed
现在是三选二，舍弃部分性质。[这里](http://www.cnblogs.com/vajoy/p/5471308.html)比较了各个k-v 支持 CAP 的程度。
查找慢写快适合一些场景比如log写入，要查找快就只能 map-reduce 了？或者 SSD。所以对于一般业务对读写没有特别要求还是 mysql 这种在线数据库安逸点，即便是 facebook 也用 mysql，号称世界最大，当然他还混合很多其他技术。对于facebook大多数业务特别是其图片，读操作是远大于写操作的，所以其用了大量的 cache 来提高性能。[Scaling Memcache At Facebook 翻译](https://www.oschina.net/translate/scaling-memcache-facebook) 这里也是用 k-v 技术，不过这个k-v 就是像 redis 了，基于内存的缓存系统。

Google Cloud Spanner https://cloud.google.com/spanner/?authuser=1&hl=zh-cn 
第一种可横向扩展、高度一致的关系型数据库服务，只有商业数据库这么大而全，比如CosmosDB。
这种在大数据量的情况下如何保证网络带宽？还是整套方案都用它的？原来这些统一叫 NewSQL
[NewSQL究竟新在哪里？](http://oserror.com/distributed/newsql/)
[「企业上云」系列之开源数据库的现状](http://www.infoq.com/cn/articles/situation-of-the-open-source-database)，TiKV 作者，也比较了很多各种类型的数据库。
https://aws.amazon.com/cn/products/databases/ AWS 产品很多，类似的似乎是Aurora，关系数据库，还有 RDS。https://www.percona.com/blog/2018/07/17/when-should-i-use-amazon-aurora-and-when-should-i-use-rds-mysql/ 区别在于 RDS 对 MYSQL 引擎兼容性更好
阿里的RDB似乎也是newsql https://cn.aliyun.com/product/rds
[NoSQL没落了？NewSQL有机会挑大梁吗？](http://database.51cto.com/art/201901/590241.htm) 解释了一堆其他的 newsql 数据库，从这些项目的实现架构来看，主要分为两种：
- 原生实现：如CockroachDB、YugaByteDB
- NoSQL+ACID+SQL：如TiDB、Trafodion

cockroachdb和 tidb 一样是基于 rocksdb 啊，为什么不同呢？这个参考是 tidb 老版本基于 hbase 的？[刘奇：如何使用HBase构建NewSQL？](https://cloud.tencent.com/developer/article/1041887) 老版本的 tidb 是基于 hbase 的吧，现在应该已经换了。[HBase进化之从NoSQL到NewSQL，凤凰涅槃成就Phoenix](https://yq.aliyun.com/articles/680772) 用的还是官方项目phoenix。可惜 hbase 是 java 的并且运行在 hadoop 上面，依赖带笨重了。

[一分钟搞懂列式与行式数据库](http://zhuanlan.51cto.com/art/201703/535729.htm)
行式更适合OLTP，比如传统的基于增删改查操作的应用。列式更适合OLAP，非常适合于在数据仓库领域发挥作用，比如数据分析、海量存储和商业智能;涉及不经常更新的数据。
Hbase 列式存储，传统的关系型数据库，如Oracle、DB2、MySQL、SQL SERVER等采用行式存储法，TiDB 典型的 OLTP 行存数据库

[TiDB HTAP 深度解读](https://my.oschina.net/zhaiyuan/blog/4604565) "TP 和 AP 传统来说仰赖不同的存储格式：行存对应 OLTP，列存对应 OLAP。" "使用写优化方式存储变更数据，然后逐步将更新部分归并到读优化的主列存区" 
[如何看待王垠对数据库的理解？](https://www.zhihu.com/question/329153374/answer/717090086)
[我们对比了5款数据库，告诉你NewSQL的独到之处](https://mp.weixin.qq.com/s/AcuFiHgRJg2OcNGtfjRxYA)
https://mariadb.com/kb/en/library/mariadb-columnstore/ 传统的数据库也在演变

### OLTP和OLAP的区别
联机事务处理OLTP（on-line transaction processing） 主要是执行基本日常的事务处理，比如数据库记录的增删查改。比如在银行的一笔交易记录，就是一个典型的事务。OLTP的特点一般有：
1. 实时性要求高。我记得之前上大学的时候，银行异地汇款，要隔天才能到账，而现在是分分钟到账的节奏，说明现在银行的实时处理能力大大增强。
2. 数据量不是很大，生产库上的数据量一般不会太大，而且会及时做相应的数据处理与转移。
3. 交易一般是确定的，比如银行存取款的金额肯定是确定的，所以OLTP是对确定性的数据进行存取
4. 高并发，并且要求满足ACID原则。比如两人同时操作一个银行卡账户，比如大型的购物网站秒杀活动时上万的QPS请求。
联机分析处理OLAP（On-Line Analytical Processing） 是数据仓库系统的主要应用，支持复杂的分析操作，侧重决策支持，并且提供直观易懂的查询结果。典型的应用就是复杂的动态的报表系统。OLAP的特点一般有：
1. 实时性要求不是很高，比如最常见的应用就是天级更新数据，然后出对应的数据报表。
2. 数据量大，因为OLAP支持的是动态查询，所以用户也许要通过将很多数据的统计后才能得到想要知道的信息，例如时间序列分析等等，所以处理的数据量很大;
3. OLAP系统的重点是通过数据提供决策支持，所以查询一般都是动态，自定义的。所以在OLAP中，维度的概念特别重要。一般会将用户所有关心的维度数据，存入对应数据平台。
总结：
OLTP即联机事务处理，就是我们经常说的关系数据库，增删查改就是我们经常应用的东西，这是数据库的基础；TPCC(Transaction Processing Performance Council)属于此类。
OLAP即联机分析处理，是数据仓库的核心部心，所谓数据仓库是对于大量已经由OLTP形成的数据的一种分析型的数据库，用于处理商业智能、决策支持等重要的决策信息；数据仓库是在数据库应用到一定程序之后而对历史数据的加工与分析，读取较多，更新较少，TPCH属于此类。
随着大数据时代的到来，对于OLAP，列存储模式或者说nosql模式比传统意义的行存储模式可能更具优势。

### Think
* 对于私有云来说，每个微服务趋向自己创建数据库，或者自己维护的大的数据库。
* 对于公有云来说，每个微服务会直接使用已有的多租户数据库。
* 从技术上来说当然是公有云多租户数据库更好（技术、经济），但是国内环境下，自己维护的更多，这种情况下，NewSQL 对于升级换代吸引很大。