---
title: flink join
date: 2022-01-05 16:52:01
tags: learn
categories: flink
---

> 使用 SQL 进行数据分析的过程中，join 是经常要使用的操作。
> 
> 在离线场景中，join 的数据集是有边界的，可以缓存数据有边界的数据集进行查询，有Nested Loop/Hash Join/Sort Merge Join 等多表 join；
> 
> 而在实时场景中，join 两侧的数据都是无边界的数据流，所以缓存数据集对长时间 job 来说，存储和查询压力很大，另外双流的到达时间可能不一致，造成 join 计算结果准确度不够；因此，Flink SQL 提供了多种 join 方法，来帮助用户应对各种 join 场景。

### regular join

> regular join 是最通用的 join 类型，不支持时间窗口以及时间属性，任何一侧数据流有更改都是可见的，直接影响整个 join 结果。如果有一侧数据流增加一个新纪录，那么它将会把另一侧的所有的过去和将来的数据合并在一起，因为 regular join 没有剔除策略，这就影响最新输出的结果; 正因为历史数据不会被清理，所以 regular join 支持数据流的任何更新操作。
> 
> 对于 regular join 来说，更适合用于离线场景和小数据量场景。

- 语法
  
  ```sql
  SELECT columns
  FROM t1  [AS <alias1>]
  [LEFT/INNER/FULL OUTER] JOIN t2
  ON t1.column1 = t2.key-name1
  ```
  
  

### interval join

> 相对于 regular join，interval Join 则利用窗口的给两个输入表设定一个 Join 的时间界限，超出时间范围的数据则对 join 不可见并可以被清理掉，这样就能修正 regular join 因为没有剔除数据策略带来 join 结果的误差以及需要大量的资源。
> 
> 但是使用interval join，需要定义好时间属性字段，可以是计算发生的 Processing Time，也可以是根据数据本身提取的 Event Time；如果是定义的是 Processing Time，则Flink 框架本身根据系统划分的时间窗口定时清理数据；如果定义的是 Event Time，Flink 框架分配 Event Time 窗口并根据设置的 watermark 来清理数据。

- 语法1
  
  ```sql
  SELECT columns
  FROM t1  [AS <alias1>]
  [LEFT/INNER/FULL OUTER] JOIN t2
  ON t1.column1 = t2.key-name1 AND t1.timestamp BETWEEN t2.timestamp  AND  BETWEEN t2.timestamp + + INTERVAL '10' MINUTE;
  ```

- 语法2
  
  ```sql
  SELECT columns
  FROM t1  [AS <alias1>]
  [LEFT/INNER/FULL OUTER] JOIN t2
  ON t1.column1 = t2.key-name1 AND t2.timestamp <= t1.timestamp and t1.timestamp <=  t2.timestamp + + INTERVAL ’10' MINUTE ;
  ```
  
  

### temproal table join

> interval Join 提供了剔除数据的策略，解决资源问题以及计算更加准确，这是有个前提：join 的两个流需要时间属性，需要明确时间的下界，来方便剔除数据；
> 
> 显然，这种场景不适合维度表的 join，因为维度表没有时间界限，对于这种场景，Flink 提供了 temproal table join 来覆盖此类场景。
> 
> 在 regular join和interval join中，join 两侧的表是平等的，任意的一个表的更新，都会去和另外的历史纪录进行匹配，temproal table 的更新对另一表在该时间节点以前的记录是不可见的。

- 语法
  
  ```sql
  SELECT columns
  FROM t1  [AS <alias1>]
  [LEFT] JOIN t2 FOR SYSTEM_TIME AS OF t1.proctime [AS <alias2>]
  ON t1.column1 = t2.key-name1
  ```
  
  
