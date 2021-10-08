---
title: ignite snippet
date: 2019-11-01 12:01:30
tags: snippet
categories: ignite
---

## ignite-2.7.0

-  编译ignite-core模块的时候需要使用jdk8，因为会报jdk.internal.misc.SharedSecrets找不到的错误

  原因：估计是在GridUnsafe.java中的miscPackage方法以及javaNioAccessObject的`Class<?> cls = Class.forName(pkgName + ".misc.SharedSecrets");`出现的问题，jdk11中SharedSecrets出现的位置是`jdk.internal.access.SharedSecrets`



### 问题

#### 1. BinaryObjectException: Conflicting enum values

- 原因

  ```
  存入ignite的数据格式
  key: String , value: Map<Enum, BigDecimal>
  Enum类型包含
  {
    A,
    B,
    C
  }
  
  在之后由于业务变更，需要新增新的enum项目，并添加D在A与B之间
  {
      A,
      D,
      B,
      C
  }
  ```

- 分析

  ```
  由于在数据存入ignite之后，ignite会保存数据相关的schema信息,此时在enum项目之间修改item，会打乱之前的index
  ```

- 解决 

  ```
  方法一：
  更改enum类的名称，不再使用原有的schema信息
  方法二：
  enum类新增项目时，需要在最后面添加，避免打乱已有的schema索引
  方法三（未验证）：
  删除 $IGNITE_HOME/work/binary_meta/Nodex里面的文件
  ```

- 官方说明

  > - You cannot change the types of existing fields.
  > - You cannot change the order of enum values or add new constants at the beginning or in the middle of the list of enum’s values. You can add new constants to the end of the list though.





### 持久化

- Ignite的原生持久化会在磁盘上存储一个数据的超集，以及根据容量在内存中存储一个子集。比如，如果有100个条目，然后内存只能存储20条，那么磁盘上会存储所有的100条，然后为了提高性能在内存中缓存20条。
- 和纯内存的使用场景一样，当打开持久化时，每个独立的节点只会持久化数据的一个子集，不管是主还是备节点，都是只包括节点所属的分区的数据，总的来说，整个集群包括了完整的数据集。
- 在开发应用时可能需要修改自定义对象字段的类型。例如假设对象A的字段类型`A.range`为`int`类型，然后决定将`A.range`的类型修改为`long`类型。之后会发现集群或应用将无法重启，因为Ignite不支持字段/列类型的更改。
- 无法变更枚举值的顺序，也无法在枚举值列表的开始或者中部添加新的常量，但是可以在列表的末尾添加新的常量。



#### ZooKeeper Discovery

ZooKeeper Discovery是为需要保持易扩展性和线性性能的大规模部署而设计的。然而，同时使用Ignite和ZooKeeper需要配置和管理两个分布式系统，这可能是一个挑战。因此，我们建议你只有在计划扩展到100个或1000个节点时才使用ZooKeeper Discovery。否则，最好使用TCP/IP发现。