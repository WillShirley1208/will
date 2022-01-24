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
