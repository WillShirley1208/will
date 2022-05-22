---
title: ignite snippet
date: 2019-11-01 12:01:30
tags: snippet
categories: ignite
---

### ignite-2.7.0

- 编译ignite-core模块的时候需要使用jdk8，因为会报jdk.internal.misc.SharedSecrets找不到的错误
  
  原因：估计是在GridUnsafe.java中的miscPackage方法以及javaNioAccessObject的`Class<?> cls = Class.forName(pkgName + ".misc.SharedSecrets");`出现的问题，jdk11中SharedSecrets出现的位置是`jdk.internal.access.SharedSecrets`

### 持久化

- Ignite的原生持久化会在磁盘上存储一个数据的超集，以及根据容量在内存中存储一个子集。比如，如果有100个条目，然后内存只能存储20条，那么磁盘上会存储所有的100条，然后为了提高性能在内存中缓存20条。
- 和纯内存的使用场景一样，当打开持久化时，每个独立的节点只会持久化数据的一个子集，不管是主还是备节点，都是只包括节点所属的分区的数据，总的来说，整个集群包括了完整的数据集。
- 在开发应用时可能需要修改自定义对象字段的类型。例如假设对象A的字段类型`A.range`为`int`类型，然后决定将`A.range`的类型修改为`long`类型。之后会发现集群或应用将无法重启，因为Ignite不支持字段/列类型的更改。
- 无法变更枚举值的顺序，也无法在枚举值列表的开始或者中部添加新的常量，但是可以在列表的末尾添加新的常量。

### ZooKeeper Discovery

ZooKeeper Discovery是为需要保持易扩展性和线性性能的大规模部署而设计的。然而，同时使用Ignite和ZooKeeper需要配置和管理两个分布式系统，这可能是一个挑战。因此，我们建议你只有在计划扩展到100个或1000个节点时才使用ZooKeeper Discovery。否则，最好使用TCP/IP发现。

----

### 目录结构

- marshaller
  
  >  $IGNITE_HOME/work/db/marshaller    类classpath信息

- binary_meta
  
  > $IGNITE_HOME/work/db/binary_meta    类的元素信息

**下面三个配置项是在配置文件中进行配置**

- storagePath
  
  > $RDX_HOME/data/ignite/persistent    持久化文件

- walPath
  
  > $RDX_HOME/data/ignite/wal_store    持久化数据的元数据信息（类名、元素名、位置）
  
  - walPath和storagePath存储的数据的关联

- walArchivePath
  
  > $RDX_HOME/data/ignite/wal_archive
  
  - 和walPath存储的数据结构一样，二者的关系

### Ignite and ZooKeeper Configuration Considerations

- zookeeper中 tickTime和syncLimit参数的定义

When using ZooKeeper Discovery, you need to make sure that the configuration parameters of the ZooKeeper cluster and Ignite cluster match each other.

Consider a sample ZooKeeper configuration, as follows:

```xml
# The number of milliseconds of each tick
tickTime=2000

# The number of ticks that can pass between sending a request and getting an acknowledgement
syncLimit=5
```

- 在zookeeper中 tickTime和syncLimit参数的作用

Configured this way, ZooKeeper server detects its own segmentation from the rest of the ZooKeeper cluster only after `tickTime * syncLimit` elapses. Until this event is detected at ZooKeeper level, all Ignite nodes connected to the segmented ZooKeeper server do not try to reconnect to the other ZooKeeper servers.

- 在ignite中 sessionTimeout参数与zookeeper的tickTime和syncLimit参数的关联

On the other hand, there is a `sessionTimeout` parameter on the Ignite side that defines how soon ZooKeeper closes an Ignite node’s session if the node gets disconnected from the ZooKeeper cluster. If `sessionTimeout` is smaller than `tickTime * syncLimit` , then the Ignite node is notified by the segmented ZooKeeper server too late — its session expires before it tries to reconnect to other ZooKeeper servers.

To avoid this situation, `sessionTimeout` should be bigger than `tickTime * syncLimit`.
