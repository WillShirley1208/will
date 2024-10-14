---
title: flink runtime framework
date: 2020-10-01 22:00:30
tags: learn
categories: flink
---

# 运行时框架

![](/images/flink/flink_runtime_architecture.png)

# 作业调度框架

![](/images/flink/flink_schedule_architecture.png)

# table模块架构

![](/images/flink/flink_table_architecture.png)

# 进程

## TaskExecutor

1. `taskmanager.numberOfTaskSlots`：指定每个TaskExecutor的任务槽数量。默认情况下，该属性设置为1，表示每个TaskExecutor只能执行一个任务。如果需要在同一节点上并行执行多个任务，则需要将该属性设置为大于1的值。
2. `taskmanager.memory.process.size`：指定TaskExecutor进程的内存大小。默认情况下，该属性设置为1GB。可以根据节点的资源情况和作业的内存需求进行适当调整。
3. `taskmanager.cpu.cores`：指定TaskExecutor可以使用的CPU核心数量。默认情况下，该属性设置为所有可用的CPU核心数。可以根据节点的CPU资源情况和作业的CPU需求进行适当调整。
4. `taskmanager.tmp.dirs`：指定TaskExecutor使用的临时文件目录。默认情况下，该属性设置为系统的临时目录。可以根据节点的磁盘空间情况和作业的磁盘需求进行适当调整。

# 目录结构

- flink-annotations: Flink自定义的一些注解，用于配置、控制编译等功能。
- flink-clients: Flink客户端，用于向Flink集群提交任务、查询状态等。其中org.apache.flink.client.cli.CliFrontend就是执行./flink run的入口。
- flink-connectors: Flink连接器，相当于Flink读写外部系统的客户端。这些连接器指定了外部存储如何作为Flink的source或sink。例如对于kafka来说，flink-connector-kafka-xx定义了FlinkKafkaConsumer和FlinkKafkaProducer类分别作为Flink的source和sink，实现了对kafka消费和生产的功能。从图二可以看出，flink 1.9目前支持的外部存储有Cassandra、ES、Kafka、Hive等一些开源外部存储。
- flink-container: Flink对docker和kubernetes的支持。
- flink-contrib: 社区开发者提供的一些新特性。
- flink-core: Flink核心的API、类型的定义，包括底层的算子、状态、时间的实现，是Flink最重要的部分。Flink内部的各种参数配置也都定义在这个模块的configuration中。（这部分代码还没怎么看过，就不细讲了）。
- flink-dist: Flink编译好之后的jar包会放在这个文件夹下，也就是网上下载的可执行的版本。其中也包括集群启动、终止的脚本，集群的配置文件等。
- flink-docs: 这个模块并不是Flink的文档，而是Flink文档生成的代码。其中org.apache.flink.docs.configuration.ConfigOptionsDocGenerator是配置文档的生成器，修改相关配置的key或者默认值，重新运行这个类就会更新doc文件夹下的html文件。同样org.apache.flink.docs.rest.RestAPIDocGenerator是Flink RestAPI文档的生成器。
- flink-fliesystems: Flink对各种文件系统的支持，包括HDFS、Azure、AWS S3、阿里云OSS等分布式文件系统。
- flink-formats: Flink对各种格式的数据输入输出的支持。包括Json、CSV、Avro等常用的格式。
- flink-java: Flink java的API，就是写flink应用时用到的map、window、keyBy、State等类或函数的实现。
- flink-jepsen: 对Flink分布式系统正确性的测试，主要验证Flink的容错机制。
- flink-libraries: Flink的高级API，包括CEP（复杂事件处理）、Gelly图处理库等。
- flink-mesos: Flink对mesos集群管理的支持。
- flink-metrics: Flink监控上报。支持上报到influxdb、prometheus等监控系统。具体的使用配置可以在flink-core模块的org.apache.flink.configuration.MetricOptions中找到。
- flink-python: Flink对python的支持，目前还比较弱。
- flink-queryable-state: Flink对可查询状态的支持，其中flink-queryable-state-runtime子模块实现了StateClientProxy和StateServer。这两部分都运行在TaskManager上，StateClientProxy负责接收外部请求，StateServe负责管理内部的queryable state。flink-queryable-state-client-java子模块实现了QueryableStateClient，作为外部系统访问queryable state的客户端。
- flink-runtime: flink运行时核心代码，在第二节细说。
- flink-runtime-web: Flink Web Dashboard的实现。默认启动standalone集群后，访问http://localhost:8081 出现的界面。
- flink-scala: Flink scala的API。
- flink-scala-shell: Flink提供的scala命令行交互接口。
- flink-state-backends: flink状态存储的方式，目前这个模块中只有RocksDBStateBackend，未来可能会支持更多种的状态存储，以适应不同的业务场景。MemoryStateBackend和FsStateBackend的实现并不在这个目录下，而是在flink-runtime目录下。
- flink-streaming-java: Flink Streaming的java API。
- flink-streaming-scala: Flink Streaming的scala API。
- flink-table: Flink Table API，在第三小节中细说。
- flink-yarn: Flink对yarn集群管理的支持。

---

- flink-runtime模块是Flink最核心的模块之一，实现了Flink的运行时框架，如JobManager、TaskManager、ResourceManager、Scheduler、Checkpoint Coordinator

- flink-table模块属于Flink的上层API，包括java和scala版本的table-api，以及SQL的解析和SQL的执行。

  > 随着Flink SQL越来越受重视，flink-table从flink-libraries中移了出来，成为了独立的一级目录。Flink 1.9中，阿里把blink-planner开源了出来，这样整个flink-table中就有了2个planner。从长期来看，流批的统一是一个趋势，因此blink-planner只使用了StreamTableEnvironment中相关的API，而没有使用BatchTableEnvironment，将批当做一个有限的流来处理，希望通过这种方式实现流和批的统一。由于blink-table-planner更好的支持流批统一，且性能更好，在未来的版本中，很有可能完全替代flink-table-planner的功能，而flink-table-planner可能将会被移除。

# checkpoint

> 基于 Chandy-Lamport 算法实现了一个分布式的一致性的快照，从而提供了一致性的语义。
>
> Chandy-Lamport 算法实际上在 1985 年的时候已经被提出来，但并没有被很广泛的应用，而 Flink 则把这个算法发扬光大了。

checkpoint是使Flink 能从故障恢复的一种内部机制。检查点是 Flink 应用状态的一个一致性副本，包括了输入的读取位点。在发生故障时，Flink 通过从检查点加载应用程序状态来恢复，并从恢复的读取位点继续处理，就好像什么事情都没发生一样。Flink的状态存储在Flink的内部,这样做的好处就是不再依赖外部系统,降低了对外部系统的依赖,在Flink的内部,通过自身的进程去访问状态变量.同时会定期的做checkpoint持久化,把checkpoint存储在一个分布式的持久化系统中,如果发生故障,就会从最近的一次checkpoint中将整个流的状态进行恢复.

# state

> 丰富的State API：ValueState、ListState、MapState、 BroadcastState

- what ?

State是指流计算过程中计算节点的中间计算结果或元数据属性，比如 在aggregation过程中要在state中记录中间聚合结果，比如 Apache Kafka 作为数据源时候，我们也要记录已经读取记录的offset，这些State数据在计算过程中会进行持久化(插入或更新)。所以Apache Flink中的State就是与时间相关的，Apache Flink任务的内部数据（计算数据和元数据属性）的快照。

- why ?

与批计算相比，State是流计算特有的，批计算没有failover机制，要么成功，要么重新计算。流计算在 大多数场景 下是增量计算，数据逐条处理（大多数场景)，每次计算是在上一次计算结果之上进行处理的，这样的机制势必要将上一次的计算结果进行存储（生产模式要持久化），另外由于 机器，网络，脏数据等原因导致的程序错误，在重启job时候需要从成功的检查点(checkpoint，后面篇章会专门介绍)进行state的恢复。增量计算，Failover这些机制都需要state的支撑。

- how ?

## 存储实现

- 基于内存的HeapStateBackend - 在debug模式使用，不 建议在生产模式下应用；

- 基于HDFS的FsStateBackend - 分布式文件持久化，每次读写都产生网络IO，整体性能不佳；

- 基于RocksDB的RocksDBStateBackend - 本地文件+异步HDFS持久化；

  > Apache Flink版本选择用RocksDB+HDFS的方式进行State的存储，State存储分两个阶段，首先本地存储到RocksDB，然后异步的同步到远程的HDFS。 这样而设计既消除了HeapStateBackend的局限（内存大小，机器坏掉丢失等），也减少了纯分布式存储的网络IO开销。

- 还有一个是基于Niagara(Alibaba内部实现)NiagaraStateBackend - 分布式持久化- 在Alibaba生产环境应用；

## 分类

通过算子和数据层面划分

- 算子类state

  KeyedState - 这里面的key是我们在SQL语句中对应的GroupBy/PartitioneBy里面的字段，key的值就是groupby/PartitionBy字段组成的Row的字节数组，每一个key都有一个属于自己的State，key与key之间的State是不可见的

- 数据类state

  OperatorState - Apache Flink内部的Source Connector的实现中就会用OperatorState来记录source数据读取的offset。

## MemoryStateBackend

1 基于内存的状态管理器，聚合类算子的状态会存储在JobManager的内存中

2 单次状态大小默认最大被限制为5MB，可以通过构造函数来指定状态初始化内存大小。无论单次状态大小最大被限制为多少，都不可大于akka的frame大小（1.5MB，JobManager和TaskManager之间传输数据的最大消息容量）。状态的总大小不能超过 JobManager 的内存。

3 是Flink默认的后端状态管理器，默认是异步的

4 主机内存中的数据可能会丢失，任务可能无法恢复

5 将工作state保存在TaskManager的内存中，并将checkpoint数据存储在JobManager的内存中

适用：本地开发和调试、状态比较少的作业



## FsStateBackend

1 基于文件系统的状态管理器（这里的文件系统可以是本地**共享**文件系统，也可以是hdfs分布式文件系统。）

2 如果使用，默认是异步

3 比较稳定，3个副本，比较安全。不会出现任务无法恢复等问题

4 状态大小受磁盘容量限制

5 将工作state保存在TaskManager的内存中，并将checkpoint数据存储在文件系统中

适用：状态比较大，窗口比较长，大的KV状态



## RocksDBStateBackend

1. RocksDBStateBackend 采用异步的方式进行状态数据的 Snapshot， 任务中的状态数据首先被写人 RocksDB 中，然后再异步地将状态数据写人文件系统中，这样在rocksDB中仅会存储正在进行计算的热数据，对于长时间才更新的数据则写人磁盘中进行存储。而对于体量比较小的元数据状态，则直接存储在 JobManager 的内存中。

2. 与 FsStateBackend 相比，RocksDBState Backend 在性能上要比 FsStateBackend 高一些，主要是因为借助于 RocksDB 存储了最新热数据，然后通过异步的方式再同步到文件系统中，但 RocksDBState Backend 和 Memory State Backend 相比性能就会较弱一些。

3. 需要注意的是ROCksDB通过JNI的方式进行数据的交互，而JNI构建在byte[]数据结构之上，因此每次能够传输的最大数据量为2^31字节，也就是说每次在RocksDBState Backend 合并的状态数据量大小不能超过 2^31 字节限制，否则将会导致状态数据无法同步，这是RocksDB 采用JNI 方式的限制，用户在使用过程中应当注意。

综上可以看出，RocksDBState Backend 和 FsState Backend 一样，适合于任务状态数据非常大的场景。在Flink 最新版本中，已经提供了基于 RocksDBState Backend 实现的增量 Checkpoints 功能，极大地提高了状态数据同步到介质中的效率和性能，在后续的社区发展中，RocksDBStateBackend 也会作为状态管理器重点使用的方式之一。

# time

> 实现了 Watermark 的机制，能够支持基于事件的时间的处理，或者说基于系统时间的处理，能够容忍数据的延时、容忍数据的迟到、容忍乱序的数据。

![](/images/flink/flink_time.png)

- Event Time：是事件创建的时间。它通常由事件中的时间戳描述，例如采集的日志数据中，每一条日志都会记录自己的生成时间，Flink 通过时间戳分配器访问事件时间戳。

- Ingestion Time：是数据进入 Flink 的时间。

- Processing Time：是每一个执行基于时间操作的算子的本地系统时间，与机器相关，默认的时间属性就是 Processing Time。

  > 例如，一条日志进入 Flink 的时间为 2019-08-12 10:00:00.123，到达 Window 的系统时间为 2019-08-12 10:00:01.234，日志的内容如下：
  >
  > 2019-08-02 18:37:15.624 INFO Fail over to rm2
  >
  > 对于业务来说，要统计 1min 内的故障日志个数，哪个时间是最有意义的？—— eventTime，因为我们要根据日志的生成时间进行统计。

# window

> Flink 提供了开箱即用的各种窗口，比如滑动窗口、滚动窗口、会话窗口以及非常灵活的自定义的窗口。

![](/images/flink/flink_window.png)

- 滚动窗口（Tumbling Window）

  将数据依据固定的窗口长度对数据进行切片, 滚动窗口分配器将每个元素分配到一个指定窗口大小的窗口中，滚动窗口有一个固定的大小，并且不会出现重叠

  特点：**时间对齐，窗口长度固定，没有重叠**

  适用场景：适合做 BI 统计等（做每个时间段的聚合计算）

  例如：如果你指定了一个 5 分钟大小的滚动窗口，窗口的创建如下图所示：

  ![](/images/flink/flink_window_tumbling.png)

- 滑动窗口（Sliding Window）

  滑动窗口是固定窗口的更广义的一种形式，滑动窗口由固定的窗口长度和滑动间隔组成。

  特点：**时间对齐，窗口长度固定，有重叠**

  滑动窗口分配器将元素分配到固定长度的窗口中，与滚动窗口类似，窗口的大小由窗口大小参数来配置，另一个窗口滑动参数控制滑动窗口开始的频率。因此，滑动窗口如果滑动参数小于窗口大小的话，窗口是可以重叠的，在这种情况下元素会被分配到多个窗口中。

  适用场景：对最近一个时间段内的统计（求某接口最近 5min 的失败率来决定是否要报警）。

  例如，你有 10 分钟的窗口和 5 分钟的滑动，那么每个窗口中 5 分钟的窗口里包含着上个 10 分钟产生的数据，如下图所示：

  ![](/images/flink/flink_window_sliding.png)

- 会话窗口（Session Window）

  由一系列事件组合一个指定时间长度的 timeout 间隙组成，类似于 web 应用的 session，也就是一段时间没有接收到新数据就会生成新的窗口。

  特点：**时间无对齐**

  session 窗口分配器通过 session 活动来对元素进行分组，session 窗口跟滚动窗口和滑动窗口相比，不会有重叠和固定的开始时间和结束时间的情况，相反，当它在一个固定的时间周期内不再收到元素，即非活动间隔产生，那个这个窗口就会关闭。一个 session 窗口通过一个 session 间隔来配置，这个 session 间隔定义了非活跃周期的长度，当这个非活跃周期产生，那么当前的 session 将关闭并且后续的元素将被分配到新的 session 窗口中去。

​        ![](/images/flink/flink_window_session.png)

---

flink API

![](/images/flink/flink_runtime.png)

> Flink 分别提供了面向流式处理的接口（DataStream API）和面向批处理的接口（DataSet API）。因此，Flink 既可以完成流处理，也可以完成批处理。Flink 支持的拓展库涉及机器学习（FlinkML）、复杂事件处理（CEP）、以及图计算（Gelly），还有分别针对流处理和批处理的 Table API。



# cdc

[Flink Forward Aisa 系列专刊｜Flink CDC 新一代数据集成框架 - 技术原理、入门与生产实践-阿里云开发者社区](https://developer.aliyun.com/article/848448?spm=a2c6h.12873639.0.d102020001.6a5a2de1EwwX6V&utm_content=g_1000316418)

# cep

- 应用场景

  ```
  风险控制
  对用户异常行为模式进行实时检测，当一个用户发生了不该发生的行为，判定这个用户是不是有违规操作的嫌疑。
  
  策略营销
  用预先定义好的规则对用户的行为轨迹进行实时跟踪，对行为轨迹匹配预定义规则的用户实时发送相应策略的推广。
  
  运维监控
  灵活配置多指标、多依赖来实现更复杂的监控模式。
  ```

# streaming warehouse

流式数仓（Streaming Warehouse）更准确地说，其实是“make data warehouse streaming”，就是让整个数仓的数据全实时地流动起来，且是以纯流的方式而不是微批（mini-batch）的方式流动。

目标是实现一个具备端到端实时性的纯流服务（Streaming Service），用一套 API 分析所有流动中的数据，当源头数据发生变化，比如捕捉到在线服务的 Log 或数据库的 Binlog 以后，就按照提前定义好的 Query 逻辑或数据处理逻辑，对数据进行分析，分析后的数据落到数仓的某一个分层，再从第一个分层向下一个分层流动，然后数仓所有分层会全部流动起来，最终流到一个在线系统里，用户可以看到整个数仓的全实时流动效果。

在这个过程中，数据是主动的，而查询是被动的，分析由数据的变化来驱动。同时在垂直方向上，对每一个数据明细层，用户都可以执行 Query 进行主动查询，并且能实时获得查询结果。此外，它还能兼容离线分析场景，API 依然是同一套，实现真正的一体化。



# Flink on YARN模式

在这种模式下Flink的资源由YARN来进行管理，Flink服务被提交到YARN的ResourceManager后，YARN的NodeManager会为Flink生成对应的容器，Flink再将JobManager和TaskManager实例部署到容器中。在这种情况下Flink可以通过JobManager所需要的slots数量来动态的调整TaskManager的资源，达到了资源的可拓展性。Flink官方也推荐正式的生产环境使用这种部署模式。
在YARN上，又分为三种部署模式：

## Session Mode

共享JobManager和TaskManager，所有提交的任务都在一个集群中运行，集群的生命周期独立于任务，任务的开始、结束不影响集群的生命周期。类似于上面的Standalone-cluster模式，任务与任务之间不隔离，共享同一套资源。

## Per-Job Mode

为每个任务创建单独的JobManager和TaskManager集群，每个任务之间互相隔离互不干扰，集群的生命周期随着任务的生命周期结束而结束。这种模式的优点就是任务独占一个集群，资源的隔离性好。

## Application Mode

一个Application可以存在多个任务，这时YARN为每个Application创建集群，Application中的任务共享该集群，资源的隔离是Application级别的，集群的生命周期随着Application的生命周期结束。这种模式更像是Session Mode和Pre-Job Mode的折中方案，既做到了资源的隔离，又提高了任务之间资源的利用率。



## interaction

![](/images/flinkOnYarn/flink_on_yarn.png) 

## two way to submit job on yarn

![](/images/flinkOnYarn/submit_job.png)

### first way：yarn session

> (Start a long-running Flink cluster on YARN)这种方式需要先启动集群，然后在提交作业，接着会向yarn申请一块空间后，资源永远保持不变。如果资源满了，下一个作业就无法提交，只能等到yarn中的其中一个作业执行完成后，释放了资源，那下一个作业才会正常提交.
>
> ps:所有作业共享Dispatcher和ResourceManager；共享资源；适合规模小执行时间短的作业.适用于本地测试或者开发

#### mode one: 客户端模式

> 可以启动多个yarn session，一个yarn session模式对应一个JobManager,并按照需求提交作业，同一个Session中可以提交多个Flink作业。如果想要停止Flink Yarn Application，需要通过yarn application -kill命令来停止.

```shell
bin/yarn-session.sh -n 2 -jm 1024 -tm 4096 -s 6
```

- YarnSessionClusterEntrypoint进程

  代表本节点可以命令方式提交job，而且可以不用指定-m参数。

  - 本节点提交任务

    `bin/flink run ~/flink-demo-wordcount.jar`

  - 如果需要在其他主机节点提交任务

    `bin/flink run -m vmhome10.com:43258 examples/batch/WordCount.jar`

- FlinkYarnSessionCli进程

  代表yarn-session集群入口，实际就是jobmanager节点，也是yarn的ApplicationMaster节点。

#### mode two: 分离式模式

> JobManager的个数只能是一个，同一个Session中可以提交多个Flink作业。如果想要停止Flink Yarn Application，需要通过yarn application -kill命令来停止。通过-d指定分离模式.

```shell
./bin/yarn-session.sh -nm test3 -d
```

> 在所有的节点只会出现一个 YarnSessionClusterEntrypoint进程

### second way: flink run

> 直接在YARN上提交运行Flink作业(Run a Flink job on YARN)，这种方式的好处是一个任务会对应一个job,即没提交一个作业会根据自身的情况，向yarn申请资源，直到作业执行完成，并不会影响下一个作业的正常运行，除非是yarn上面没有任何资源的情况下。
>
> ps:适用于生产环境，可启动多个yarn session （bin/yarn-session.sh -nm ipOrHostName）

```shell
./bin/flink run -m addressOfJobmanager -yn 1 -yjm 1024 -ytm 1024 ./examples/batch/WordCount.jar
```

注意使用参数-m yarn-cluster提交到yarn集群。

- 运行到指定的yarn session

  可以指定 -yid,--yarnapplicationId <arg> Attach to running YARN session来附加到到特定的yarn session上运行

---



reference

[Flink on yarn部署模式 - 简书](https://www.jianshu.com/p/1b05202c4fb6)

[flink on yarn模式下两种提交job方式 - 我是属车的 - 博客园](https://www.cnblogs.com/asker009/p/11327533.html)

[Flink-On-Yarn的部署模式](https://blog.csdn.net/u013411339/article/details/95421500?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164455981216780357293300%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=164455981216780357293300&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-21-95421500.nonecase&utm_term=%E2%80%9Cflink+cep%E2%80%9D&spm=1018.2226.3001.4450)



# FAQ

## 依赖冲突

https://flink.apache.org/getting-help/#i-see-a-classcastexception-x-cannot-be-cast-to-x

https://nightlies.apache.org/flink/flink-docs-release-1.17/docs/ops/debugging/debugging_classloading

排查办法

- 使用arthas定位依赖类所在jar包

解决办法

- 对冲突jar包使用maven shaded 的exclude或relocation 进行操作

- 使用7zip等解压软件对jar文件解压删除（人工shaded）

