---
layout: post
title:  "大数据各开源框架简介"
date:   2017-05-02 14:20:33
categories:
  - bigdata
  - overview
---

https://hadoopecosystemtable.github.io/ 英文的，很全面。

https://hortonworks.com/ecosystems/ 整个生态系统

Apache Accumulo 是一个可靠的、可伸缩的、高性能的排序分布式的 Key-Value 存储解决方案，基于单元访问控制以及可定制的服务器端处理。使用 Google BigTable 设计思路，基于 Apache Hadoop、Zookeeper 和 Thrift 构建
HDP 里面有 web ui，有 table，为什么要这个？给谁用？
这个和 leveldb 一样的数据库，强调速度快。

Hue是一个开源的Apache Hadoop UI系统，由Cloudera Desktop演化而来，最后Cloudera公司将其贡献给Apache基金会的Hadoop社区，它是基于Python Web框架Django实现的。通过使用Hue我们可以在浏览器端的Web控制台上与Hadoop集群进行交互来分析处理数据，例如操作HDFS上的数据，运行MapReduce Job，执行Hive的SQL语句，浏览HBase数据库等等。

thrift是一个软件框架，用来进行可扩展且跨语言的服务的开发。它结合了功能强大的软件堆栈和代码生成引擎，以构建在 C++, Java, Go,Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk, and OCaml 这些编程语言间无缝结合的、高效的服务。

Avro 也是序列化框架，不过不用根据 schema 生成代码，这样可以直接在脚本语言中使用。用于支持大批量数据交换和程序间调用。http://langyu.iteye.com/blog/708568

Atlas 是一个可伸缩和可扩展的核心功能治理服务。企业可以利用它高效的管理 Hadoop 以及整个企业数据生态的集成。

Apache Oozie 工作流调度，Workflow 只是最基础的，Coordinator：多个WorkFlow可以组成一个Coordinator，可以把前几个WorkFlow的输出作为后一个WorkFlow的输入，当然也可以定义WorkFlow的触发条件，来做定时触发，Bundle：控制一个或多个Coordinator应用。分别对应 v1, v2, v3 三个版本。CDAP Workflows: In Comparison with Apache Oozie。

Apache Falcon是一个面向Hadoop的、新的数据处理和管理平台，设计用于数据移动、数据管道协调、生命周期管理和数据发现。它使终端用户可以快速地将他们的数据及其相关的处理和管理任务“上载（onboard）”到Hadoop集群。

Apache Phoenix enables OLTP and operational analytics in Hadoop for low latency applications by combining the best of both worlds:
the power of standard SQL and JDBC APIs with full ACID transaction capabilities and
the flexibility of late-bound, schema-on-read capabilities from the NoSQL world by leveraging HBase as its backing store
Apache Phoenix is fully integrated with other Hadoop products such as Spark, Hive, Pig, Flume, and Map Reduce.

Storm
Hadoop（大数据分析领域无可争辩的王者）专注于批处理。这种模型对许多情形（比如为网页建立索引）已经足够，但还存在其他一些使用模型，它们需要来自高度动态的来源的实时信息。为了解决这个问题，就得借助 Nathan Marz 推出的 Storm（现在在 Twitter 中称为 BackType）。https://www.ibm.com/developerworks/cn/opensource/os-twitterstorm/

对于 hbase 当前 noSql 数据库的一种，最常见的应用场景就是采集的网页数据的存储，由于是key-value型数据库，可以再扩展到各种key-value应用场景，如日志信息的存储，对于内容信息不需要完全结构化出来的类CMS应用等。注意hbase针对的仍然是OLTP应用为主。不依赖 hadoop/hdfs？
对于hive主要针对的是OLAP应用，注意其底层不是hbase，而是hdfs分布式文件系统，重点是基于一个统一的查询分析层，支撑OLAP应用中的各种关联，分组，聚合类SQL语句。hive一般只用于查询分析统计，而不能是常见的CUD操作，要知道HIVE是需要从已有的数据库或日志进行同步最终入到hdfs文件系统中，当前要做到增量实时同步都相当困难。
1. Hive中的表是纯逻辑表，就只是表的定义等，即表的元数据。Hive本身不存储数据，它完全依赖HDFS和MapReduce。这样就可以将结构化的数据文件映射为为一张数据库表，并提供完整的SQL查询功能，并将SQL语句最终转换为MapReduce任务进行运行。 而HBase表是物理表，适合存放非结构化的数据。
2. Hive是基于MapReduce来处理数据,而MapReduce处理数据是基于行的模式；HBase处理数据是基于列的而不是基于行的模式，适合海量数据的随机访问，不依赖Hadoop。
3. HBase的表是疏松的存储的，因此用户可以给行定义各种不同的列；而Hive表是稠密型，即定义多少列，每一行有存储固定列数的数据。
4. Hive使用Hadoop来分析处理数据，而Hadoop系统是批处理系统，因此不能保证处理的低迟延问题；而HBase是近实时系统，支持实时查询。
5. Hive不提供row-level的更新，它适用于大量append-only数据集（如日志）的批任务处理。而基于HBase的查询，支持和row-level的更新。
6. Hive提供完整的SQL实现，通常被用来做一些基于历史数据的挖掘、分析。而HBase不适用与有join，多级索引，表关系复杂的应用场景。

http://jupyter.org/ Open source, interactive data science and scientific computing across over 40 programming languages. 和 hue 有点像，图像似乎是后台产生的。能交互式的展示大数据的数据。https://try.jupyter.org/ 这个可以直接试用。文档和代码混合，可以随时调整，通过文档和代码配合，更适合学习和交流用。

https://deeplearning4j.org/ 深度学习的 Java 版，有用到 Spark。

Apache Beam将统一大数据平台的开发 http://www.infoq.com/cn/news/2016/05/big-data-weekly-8
Apache Beam本身不是一个流式处理平台，而是一个统一的编程框架，它提供了开源的、统一的编程模型，帮助你创建自己的数据处理流水线，实现可以运行在任意执行引擎之上批处理和流式处理任务。Google 开源出来的东西。http://www.infoq.com/cn/articles/why-google-replace-beam-with-apache-mapreduce  为什么Google用Apache Beam彻底替换掉MapReduce
兼容的引擎 https://beam.apache.org/documentation/runners/capability-matrix/
http://www.infoq.com/cn/articles/why-google-replace-beam-with-apache-mapreduce 背后的理念
https://beam.apache.org/get-started/wordcount-example/

Apache Flink 的核心是一个提供了数据分发以及并行化计算的流数据处理引擎，已经支持了 API 化的 SQL 查询，包括图操作和机器学习的相关算法。 https://www.ibm.com/developerworks/cn/opensource/os-cn-apache-flink/ Quick Start看也是建立在 Hadoop 上面的一个任务管理平台。其任务之间可以交换数据流。这个框架位置和 Spark 一样，都是流式处理，但流行度还是 Spark 高。

http://bigtop.apache.org/ 类似 CDH/HDP，可以安装一整套的环境，而且也提供了 BigPetstore 作为例子来做性能评估。

Tez是Apache最新的支持DAG作业的开源计算框架，它可以将多个有依赖的作业转换为一个作业从而大幅提升DAG作业的性能。
http://dongxicheng.org/mapreduce-nextgen/apache-tez/ spark与tez比较. 类似 DAG 框架还有Oozie。

Mahout 其主要目标是创建一些可伸缩的机器学习算法，它们正好也是实际应用程序中相当常见的三个领域：协作筛选, 聚合, 分类。https://www.ibm.com/developerworks/cn/java/j-mahout/

Nutch 是一个开源Java实现的搜索引擎。它提供了我们运行自己的搜索引擎所需的全部工具。包括全文搜索和Web爬虫。

Apache Kylin 是一个开源的分布式分析引擎，提供 Hadoop/Spark 之上的SQL查询接口及多维分析（OLAP）能力以支持超大规模数据，最初由eBay Inc. 开发并贡献至开源社区。它能在亚秒内查询巨大的Hive表。国人主导开发。