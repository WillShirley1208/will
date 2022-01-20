---
title: flink Memory Model
date: 2019-10-01 22:00:30
tags: learn
categories: flink
---

## 内存模型

### jobmanager

![](/images/flink/jobmanager.jpg)

- JVM Heap大小设置对应配置文件`flink-conf.yaml`的**jobmanager.heap.size**

### taskmanager

![](/images/flink/taskmanager.jpg)

- Total Flink Memory对应`flink-conf.yaml`的**taskmanager.memory.flink.size**
- JVM Metaspace对应`flink-conf.yaml`的**taskmanager.memory.jvm-metaspace.size**
- Total Process Memory对应`flink-conf.yaml`的**taskmanager.memory.process.size**

#### Flink总内存（Total Flink Memory）

- **含义**  
  TaskManager进程占用的所有与Flink相关的内存（不包括JVM元空间和其他额外开销）。具体包含4大块：Flink框架内存（堆内、堆外）、托管内存（仅堆外）、网络缓存（仅堆外）、任务内存（堆内、堆外）。

- **参数**  
  `taskmanager.memory.flink.size`：无默认值，需要用户指定。

#### Flink框架（Framework）内存

- 含义
  Flink Runtime底层占用的内存，一般来讲相对固定，不需要更改。极特殊情况下才需要调大一些，比如非常高的算子并行度，或者与外部系统（如Hadoop）有密集交互等等。

- 参数
  
  `taskmanager.memory.framework.heap.size`：堆内部分（Framework Heap），默认值128MB；
  
  `taskmanager.memory.framework.off-heap.size`：堆外部分（Framework Off-Heap），以直接内存形式分配，默认值128MB。

#### 托管（Managed）内存

- 含义
  纯堆外内存，由MemoryManager管理，用于中间结果缓存、排序、哈希表等，以及RocksDB状态后端。可见，RocksDB消耗的内存可以由用户显式控制了，不再像旧版本一样难以预测和调节。

- 参数
  
  `taskmanager.memory.managed.fraction`：托管内存占Flink总内存`taskmanager.memory.flink.size的比例`，默认值0.4；`taskmanager.memory.managed.size`：托管内存的大小，无默认值，一般也不指定，而是依照上述比例来推定，更加灵活。

#### 网络（Network）缓存

- 含义
  纯堆外内存，用于TaskManager之间（shuffle、广播等）及与外部组件的数据传输，以直接内存形式分配。

- 参数
  
  `taskmanager.memory.network.min`：网络缓存的最小值，默认64MB；`taskmanager.memory.network.max`：网络缓存的最大值，默认1GB；`taskmanager.memory.network.fraction`：网络缓存占Flink总内存`taskmanager.memory.flink.size`的比例，默认值0.1。若根据此比例算出的内存量比最小值小或比最大值大，就会限制到最小值或者最大值。

#### 任务（Task）内存

- 含义
  顾名思义，是算子逻辑和用户代码、自定义数据结构真正占用的内存。

- 参数
  
  `taskmanager.memory.task.heap.size`：堆内部分（Task Heap），无默认值，一般不建议设置，会自动用Flink总内存减去框架、托管、网络三部分的内存推算得出。`taskmanager.memory.task.off-heap.size`：堆外部分（Task Off-Heap），以直接内存形式分配，默认值为0，即不使用。

##### TaskManager进程总内存（Total Process Memory）

- **含义**  
  在容器化部署（on YARN/K8s/Mesos）环境下使用，是Flink总内存、JVM元空间与JVM额外内存开销的和，也就是容器本身的内存大小。

- **参数**  
  `taskmanager.memory.process.size`：无默认值，需要用户指定。

##### JVM元空间（Metaspace）

- **含义** 
  存放已被虚拟机加载的**类信息、常量、静态变量，即编译器编译后的代码**

- **参数**  
  `taskmanager.memory.jvm-metaspace.size`：默认值256MB。

#### JVM额外开销（Overhead）

- 含义
  为JVM预留的其他本地内存，用于线程栈、代码缓存等，作用有些类似于之前版本中为容器预留的截断（cutoff）内存。当然在1.10版本中，原先的containerized.heap-cutoff-ratio与containerized.heap-cutoff-min参数对TM就不再生效了。

- 参数
  
  `taskmanager.memory.jvm-overhead.min`：JVM额外开销的最小值，默认192MB；`taskmanager.memory.jvm-overhead.max`：JVM额外开销的最大值，默认1GB；`taskmanager.memory.jvm-overhead.fraction`：JVM额外开销占TM进程总内存`taskmanager.memory.process.size`（注意不是Flink总内存）的比例，默认值0.1。若根据此比例算出的内存量比最小值小或比最大值大，就会限制到最小值或者最大值。



### Flink内存参数与JVM参数的关系

上述内存参数会直接影响启动TaskManager时使用的JVM参数，使用相关工具可以观察到。

- `-Xmx`/`-Xms`：对应堆内框架内存与任务内存之和；

- `-XX:MaxDirectMemorySize`：对应三块直接内存，即堆外框架内存、任务内存与网络缓存之和；

- `-XX:MaxMetaspaceSize`：对应JVM元空间设置。







## 配置优化

- **节点平均分配**
  
  ```yaml
  cluster.evenly-spread-out-slots: true
  ```

---

reference

[Flink 1.10之改进的TaskManager内存模型与配置【附源码】_wx5c7a97e3804fd_51CTO博客](https://blog.51cto.com/u_14222592/2892910)
