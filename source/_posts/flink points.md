---
title: flink points
date: 2021-12-28 18:43:00
tags: point
categories: flink
---

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

# FAQ

## 依赖冲突

https://flink.apache.org/getting-help/#i-see-a-classcastexception-x-cannot-be-cast-to-x

https://nightlies.apache.org/flink/flink-docs-release-1.17/docs/ops/debugging/debugging_classloading

排查办法

- 使用arthas定位依赖类所在jar包

解决办法

- 对冲突jar包使用maven shaded 的exclude或relocation 进行操作

- 使用7zip等解压软件对jar文件解压删除（人工shaded）
