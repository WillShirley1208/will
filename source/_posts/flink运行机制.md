---
title: flink 运行机制
date: 2020-10-01 22:00:30
tags: learn
categories: flink
---

# 运行时框架

![](/images/flink/flink_runtime_architecture.png)

# 作业调度框架

![](/images/flink/flink_schedule_architecture.png)

# table模块架构

![](/images/flink/flink_table_architecture.png)

# 进程

## TaskExecutor

1. `taskmanager.numberOfTaskSlots`：指定每个TaskExecutor的任务槽数量。默认情况下，该属性设置为1，表示每个TaskExecutor只能执行一个任务。如果需要在同一节点上并行执行多个任务，则需要将该属性设置为大于1的值。
2. `taskmanager.memory.process.size`：指定TaskExecutor进程的内存大小。默认情况下，该属性设置为1GB。可以根据节点的资源情况和作业的内存需求进行适当调整。
3. `taskmanager.cpu.cores`：指定TaskExecutor可以使用的CPU核心数量。默认情况下，该属性设置为所有可用的CPU核心数。可以根据节点的CPU资源情况和作业的CPU需求进行适当调整。
4. `taskmanager.tmp.dirs`：指定TaskExecutor使用的临时文件目录。默认情况下，该属性设置为系统的临时目录。可以根据节点的磁盘空间情况和作业的磁盘需求进行适当调整。













