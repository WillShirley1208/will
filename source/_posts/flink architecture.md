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

# points

### checkpoint

> 基于 Chandy-Lamport 算法实现了一个分布式的一致性的快照，从而提供了一致性的语义。
>
> Chandy-Lamport 算法实际上在 1985 年的时候已经被提出来，但并没有被很广泛的应用，而 Flink 则把这个算法发扬光大了。

### state

> 丰富的State API：ValueState、ListState、MapState、 BroadcastState

### time

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

### window

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











