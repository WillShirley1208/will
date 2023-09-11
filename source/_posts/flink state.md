---
title: flink state
date: 2022-01-11 14:32:39
tags: point
categories: flink
---

### what ?

State是指流计算过程中计算节点的中间计算结果或元数据属性，比如 在aggregation过程中要在state中记录中间聚合结果，比如 Apache Kafka 作为数据源时候，我们也要记录已经读取记录的offset，这些State数据在计算过程中会进行持久化(插入或更新)。所以Apache Flink中的State就是与时间相关的，Apache Flink任务的内部数据（计算数据和元数据属性）的快照。

### why ?

与批计算相比，State是流计算特有的，批计算没有failover机制，要么成功，要么重新计算。流计算在 大多数场景 下是增量计算，数据逐条处理（大多数场景)，每次计算是在上一次计算结果之上进行处理的，这样的机制势必要将上一次的计算结果进行存储（生产模式要持久化），另外由于 机器，网络，脏数据等原因导致的程序错误，在重启job时候需要从成功的检查点(checkpoint，后面篇章会专门介绍)进行state的恢复。增量计算，Failover这些机制都需要state的支撑。

### how ?

#### 存储实现

- 基于内存的HeapStateBackend - 在debug模式使用，不 建议在生产模式下应用；

- 基于HDFS的FsStateBackend - 分布式文件持久化，每次读写都产生网络IO，整体性能不佳；

- 基于RocksDB的RocksDBStateBackend - 本地文件+异步HDFS持久化；
  
  > Apache Flink版本选择用RocksDB+HDFS的方式进行State的存储，State存储分两个阶段，首先本地存储到RocksDB，然后异步的同步到远程的HDFS。 这样而设计既消除了HeapStateBackend的局限（内存大小，机器坏掉丢失等），也减少了纯分布式存储的网络IO开销。

- 还有一个是基于Niagara(Alibaba内部实现)NiagaraStateBackend - 分布式持久化- 在Alibaba生产环境应用；

#### 分类

通过算子和数据层面划分

- 算子类state
  
  KeyedState - 这里面的key是我们在SQL语句中对应的GroupBy/PartitioneBy里面的字段，key的值就是groupby/PartitionBy字段组成的Row的字节数组，每一个key都有一个属于自己的State，key与key之间的State是不可见的

- 数据类state
  
  OperatorState - Apache Flink内部的Source Connector的实现中就会用OperatorState来记录source数据读取的offset。

### checkpoint

checkpoint是使Flink 能从故障恢复的一种内部机制。检查点是 Flink 应用状态的一个一致性副本，包括了输入的读取位点。在发生故障时，Flink 通过从检查点加载应用程序状态来恢复，并从恢复的读取位点继续处理，就好像什么事情都没发生一样。Flink的状态存储在Flink的内部,这样做的好处就是不再依赖外部系统,降低了对外部系统的依赖,在Flink的内部,通过自身的进程去访问状态变量.同时会定期的做checkpoint持久化,把checkpoint存储在一个分布式的持久化系统中,如果发生故障,就会从最近的一次checkpoint中将整个流的状态进行恢复.



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
