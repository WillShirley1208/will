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

![](./img/flink/taskmanager.jpg)

- Total Flink Memory对应`flink-conf.yaml`的**taskmanager.memory.flink.size**
- JVM Metaspace对应`flink-conf.yaml`的**taskmanager.memory.jvm-metaspace.size**
- Total Process Memory对应`flink-conf.yaml`的**taskmanager.memory.process.size**

## 配置优化

- **节点平均分配**
  
  ```yaml
  cluster.evenly-spread-out-slots: true
  ```
