---
layout: post
title:  "CDAP Get Started"
date:   2016-11-24 14:20:33
typora-root-url: ../../../blog
---

产品特点：动态扩展，动态部署，实时监控，对开发人员友好（比如分 standalone 模式和分布式模式），REST 设计完整。

[官方 Get started](http://docs.cask.co/cdap/current/en/developers-manual/getting-started/index.html)

[build example](http://docs.cask.co/cdap/4.1.0/en/developers-manual/getting-started/building-apps.html) 构建样例，很有用。

[首页](https://cdap.io/)值得仔细一看
>CDAP Ingest is a repository of client SDKs, daemons and tools for ingesting data into CDAP.
>
>CDAP Apps is a repository of data applications built using CDAP. Try out the apps or, if you are interested in contributing applications, we welcome contributions.
>
>CDAP Packs is a repository of useful and reusable building blocks for your data applications. They consist of libraries for common data patterns and programs useful for building Big Data applications.

[架构的一些理念](http://www.slideshare.net/caskdata/webinar-whats-new-in-cdap-35)，其他 PPT 也可以一看。
摘要：Apache Sentry is a system for enforcing fine grained role based authorization to data and metadata stored on a Hadoop cluster.

退回[老版本](https://nodejs.org/dist/v4.4.0/)，使用 pkg 包安装更容易。

我用 zip 包的模式

他有 mvn 创建了一个应用，这个是啥东西？这个应用是项目吧

然后加载一个 artifact，然后用这个 artifact 创建一个应用，又是一个应用。这个应用在 UI 里面的Applications可以看到。

然后开始实时处理。前面没有处理任何东西么？

里面有个Hydrator的例子，拿 log 处理，很容易运行成功。

[命令行工具](http://docs.cdap.io/cdap/current/en/introduction/index.html#data-exploration)，可以查看已有的数据。

似乎其有自己的一套工具和 API，后端的同学可能不是很喜欢

<img src="/images/2016/cdap-get-started-1.png"/>
[*from here*](http://docs.cdap.io/cdap/current/en/developers-manual/building-blocks/core.html)

### CLI
<http://docs.cask.co/cdap/4.1.0/en/reference-manual/cli-api.html>
`cli->list programs`
列出所有正在运行的程序，pipeline只有 publish 后才会在这里出现。
`execute "show tables”`
列出所有的 hive 表，可以在上面运行查询。这个在 CLI 文档里面没有。

Hadoop这里是作为一个任务管理的，不是分析工具么？
ZooKeeper是做啥用？可以有多个hadoop节点？似乎 YARN 本身可以分布式(apache twill)。Router是类似任务的分发/负载均衡？

分布式可以看这里 <http://docs.cdap.io/cdap/current/en/developers-manual/overview/modes.html#runtime-modes>
他有好几种运行模式，可以参考部署架构图 <http://docs.cdap.io/cdap/current/en/admin-manual/deployment-architectures.html>

### Auth / 认证
[Basic Authentication](http://docs.cdap.io/cdap/current/en/admin-manual/security/perimeter-security.html)
可以直接明文，比如 fan: 123123，他用的是 jetty 的认证代码，参考<https://wiki.eclipse.org/Jetty/Tutorial/Realms>
服务器开启认证后，CDAP UI 服务器要重启下，他会缓存 router 的配置？
访问 router REST API 需要带一个 token ，没有的话，他会告诉你到哪里拿这个 token。
如果没有登录的话，页面等会后会跳到 login 页面。可能是在 backendstatus 轮询时检查的。
对于前端 API，需要在 header 里面加上Authorization:Bearer Agxxx，cdap/cdap-ui/app/cdap/services/resource-helper/index.js 的 apiCreator 方法会帮忙做这个事情。UI 上有菜单生成 auth token 的功能，每次得到的不同。这个 session token 存放在哪里？

### Metrics
处理日志，cdap-watch
CDAP Kafka: Metrics and logging transport service, using an embedded version of Kafka (package cdap-kafka);
<http://docs.cdap.io/cdap/current/en/admin-manual/operations/metrics.html?highlight=metrics>
现在看来，metrics 只是用来监控程序运行情况，log 似乎不在这里。都是用了 kafka？
Spark 本身也有 metrics 的概念 <http://spark.apache.org/docs/latest/monitoring.html >
metrics 有两种：count 和度量，可以自定义 <http://docs.cask.co/cdap/4.1.1/en/admin-manual/operations/metrics.html> & 
<http://blog.cask.co/2015/04/metrics-system-for-data-application-platform/>
对于任务管理系统，log & metrics 至关重要。CDAP 里面体现在：pipleline 的运行次数，这个保存在哪里？pipleline 的 log 可以分析，这个应该不是简单的 log 文件。

API <http://docs.cask.co/cdap/4.1.1/en/reference-manual/http-restful-api/metrics.html>

### Arch
router可以直接访问YARN Cluster，标准的 YARN？
standalone起来后可以看到有3个Java进程
<img src="/images/2016/cdap-get-started-2.png"/>
[*from here*](http://docs.cdap.io/cdap/current/en/admin-manual/deployment-architectures.html)

从上图分析看，应该分别为Router, Master, Kafka了。还是这个三个在一块，在加上Zookeeper和 YARN？查看StandaloneMain.java，他是用ExternalJavaProcessExecutor来启动zk和kafka这两个 Java 进程。hadoop呢？原来其standalone模式是直接命令运行，不需要起server。
在conf/cdap-site.xml里面定义了zk的端口为2181，使用[zookeeper-explorer](https://github.com/izerui/zookeeper-explorer/releases) 查看其数据。data/conf/cdap-config.json定义kafka端口为9092。

>${local.data.dir}/ldb     Database directory for LevelDB, used for data fabric in Standalone CDAP。

比如cdap-sdk-3.6.0/data/ldb/cdap_default.pluin2Table，这个是输出dataset？standalone没有用hadoop？我使用leveldb GUI 工具，没法读取。hbase-kv does not match existing comparator: leveldb.BytewiseComparator!
cdap-common/src/main/resources/cdap-default.xml中有定义:
```
  <property>
    <name>data.local.storage</name>
    <value>${local.data.dir}/ldb</value>
    <description>
      Database directory for LevelDB, used for data fabric in Standalone
      CDAP
    </description>
  </property>
```
有些测试LevelDBTableTest和LevelDBKVTableTest，但是基本没法运行。LevelDBTableService我看用的是org.iq80.leveldb作为客户端。我尝试用https://github.com/Level/levelup Node.js 客户端去读里面的数据，还是一样的错：hbase-kv does not match existing comparator : leveldb.BytewiseComparator。hbase-kv还是在LevelDBTableService里面对应，这样看来还得用iq80的库了。

stream放在cdap-sdk-3.6.0/data/namespaces/default/streams

[这个参数列表](http://docs.cdap.io/cdap/current/en/admin-manual/appendices/cdap-site.html )暴露了系统很多信息

[Guava](https://github.com/google/guava)工程包含了若干被Google的 Java项目广泛依赖 的核心库，例如：集合 [collections] 、缓存 [caching] 、原生类型支持 [primitives support] 、并发库 [concurrency libraries] 、通用注解 [common annotations] 、字符串处理 [string processing] 、I/O 等等。 代码里面大量用了 Preconditions.checkArgument 来校验入参。
guice  依赖注入...

### REST API 
多租户架构
<http://docs.cdap.io/cdap/current/en/reference-manual/http-restful-api/index.html>
<https://zh.wikipedia.org/wiki/%E5%A4%9A%E7%A7%9F%E6%88%B6%E6%8A%80%E8%A1%93>

> V3 of the API includes the namespacing of applications, data, and metadata to achieve application and data isolation. This is an inital step towards introducing multi-tenancy into CDAP.
[https://www.ibm.com/developerworks/cn/java/j-lo-dataMultitenant/](https://www.ibm.com/developerworks/cn/java/j-lo-dataMultitenant/) 普通的多租户的概念是在数据库上面的隔离级别，cdap 这个似乎是从 api 级别考虑。

API 认证 <http://docs.cdap.io/cdap/current/en/reference-manual/http-restful-api/introduction.html>

### Build
<https://github.com/caskdata/cdap/blob/develop/BUILD.rst>
可以单独编译某个包比如在 cdap 根目录下运行：
```
mvn clean package -pl cdap-proto -am -DskipTests
```
但是这也会编译依赖的项目，耗时有点长啊。

尝试运行某个 test case:
```
fanMBP:cdap-app-fabric fan$ mvn test -Dtest=co.cask.cdap.internal.app.services.http.handlers.ArtifactHttpHandlerTest
[ERROR] /Users/fan/dev/cdap/cdap-app-fabric/src/main/java/co/cask/cdap/internal/app/runtime/batch/MapReduceLifecycleContext.java:[61,8] 
co.cask.cdap.internal.app.runtime.batch.MapReduceLifecycleContext不是抽象的, 并且未覆盖co.cask.cdap.api.RuntimeContext中的抽象方法getClusterName()
```
这个是因为我们的代码是3.6.1-snapshot 的，如果在 cdap-app-fabric 下运行 mvn test，会直接下载远程库上的 3.6.1 api jar ，而后者在库中的版本已经更新了，但是 github 中并没有发布这个版本的代码，这导致代码不一致。

运行 `mvn install -DskipTests` 尝试把本地代码发布到本地的 mvn 库中。不要用 `mvn deploy`，这个会发布到远程库中。然后就可以了。

上面的这种命令行模式在 `intellij` 下面也可以正常运行，显然，对于 java，intellij 更合适了。
```
[ERROR] Failed to execute goal org.apache.rat:apache-rat-plugin:0.12:check (rat-check) on project cdap-app-fabric: Too many files with unapproved license: 2 See RAT report in: /Users/fan/dev/cdap/cdap-app-fabric/target/rat.txt -> [Help 1]
mvn clean package -DskipTests -Drat.skip=true -Drat.ignoreErrors=true

[INFO] --- maven-checkstyle-plugin:2.17:check (validate) @ cdap-app-fabric ---
[INFO] Starting audit...
[ERROR] /Users/fan/dev/cdap/cdap-app-fabric/src/main/java/co/cask/cdap/gateway/handlers/ArtifactHttpHandler.java:64: 不应使用 '.*' 形式的导入 - com.google.gson.* 。 [AvoidStarImport]
[ERROR] /Users/fan/dev/cdap/cdap-app-fabric/src/main/java/co/cask/cdap/gateway/handlers/ArtifactHttpHandler.java:449: 本行字符数 121个，最多：120个。 [LineLength]
[ERROR] /Users/fan/dev/cdap/cdap-app-fabric/src/main/java/co/cask/cdap/gateway/handlers/ArtifactHttpHandler.java:482:61: '{' 前应有空格。 [WhitespaceAround]
```
好像很牛的样子，如果在 Intellij 中打开这个，不然每次 build 时候又得改代码

禁止 Intellij 每次合并 import：<http://stackoverflow.com/questions/3348816/intellij-never-use-wildcard-imports>

Unable to find suppressions file at location: suppressions.xml: —> -Dcheckstyle.skip=true

Debug: <http://docs.cdap.io/cdap/current/en/developers-manual/testing/debugging.html>

### Dataset
Dataset 是 CDAP 数据的封装形式
<http://docs.cask.co/cdap/4.1.0/en/reference-manual/javadocs/co/cask/cdap/api/dataset/lib/AbstractDataset.html><http://docs.cdap.io/cdap/current/en/developers-manual/testing/debugging.html> 常用的有 KeyValueTable。

<http://docs.cask.co/cdap/current/en/developers-manual/building-blocks/datasets/index.html>

### Think
1. 使用 pipeline，性能会有损耗么？
2. 这种 pipeline 的模式只是数据挖掘的一个开始，然后就被抛弃了？总感觉这种拖拉的模式跟不上变化。有用的地方只是可视化。