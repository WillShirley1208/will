---
title: flink duduplication
date: 2023-08-02 14:37:35
tags: 去重
categories: flink
---

- 语法

  ```sql
  SELECT [column_list]
  
  FROM (
  
     SELECT [column_list],
  
       ROW_NUMBER() OVER ([PARTITION BY col1[, col2...]]
  
         ORDER BY col1 [asc|desc][, col2 [asc|desc]...]) AS rownum
  
     FROM table_name)
  
  WHERE rownum <= N [AND conditions]
  ```

  | 参数                               | 说明                                                         |
  | ---------------------------------- | ------------------------------------------------------------ |
  | ROW_NUMBER()                       | 计算行号的OVER窗口函数。行号从1开始计算。                    |
  | PARTITION BY col1[, col2..]        | 可选。指定分区的列，即去重的KEYS。                           |
  | ORDER BY timeAttributeCol asc desc | 指定排序的列，必须是一个的字段（即Proctime或Rowtime）。可以指定顺序（Keep FirstRow）或者倒序 （Keep LastRow）。 |
  | rownum                             | 外层查询中对排名进行过滤，只取前N条                          |

  - Deduplicate Keep FirstRow保留首行的去重策略：保留KEY下第一条出现的数据，之后出现该KEY下的数据会被丢弃掉。因为STATE中只存储了KEY数据，所以性能较优。
  - Deduplicate Keep LastRow保留末行的去重策略：保留KEY下最后一条出现的数据。因此过程中会产生变更的记录，会向下游发送变更的消息。因此，sink表需要支持retract操作。

在 Deduplication 关于是否会出现回撤流

1. ⭐ Order by 事件时间 DESC：会出现回撤流，因为当前 key 下 `可能会有` 比当前事件时间还大的数据
2. ⭐ Order by 事件时间 ASC：会出现回撤流，因为当前 key 下 `可能会有` 比当前事件时间还小的数据
3. ⭐ Order by 处理时间 DESC：会出现回撤流，因为当前 key 下 `可能会有` 比当前处理时间还大的数据
4. ⭐ Order by 处理时间 ASC：不会出现回撤流，因为当前 key 下 `不可能会有` 比当前处理时间还小的数据







---

reference

https://www.modb.pro/db/232004