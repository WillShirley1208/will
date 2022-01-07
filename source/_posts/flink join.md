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

### interval join

### temproal table join


