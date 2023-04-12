---
title: JVM Command
date: 2023-03-06 11:23:15
tags: command
categories: jvm
---

# 实践一: 查看进程参数

> 查看服务设置的jvm
>
> jps -v
>
> 查看服务jvm的默认参数
>
> jinfo -flags PID

- 参考kafka服务的运行参数

  ```shell
  -server
  -XX:+UseG1GC
  -XX:MaxGCPauseMillis=20
  -XX:InitiatingHeapOccupancyPercent=35
  -XX:+ExplicitGCInvokesConcurrent
  ```

  **-XX:MaxGCPauseMillis=200**

  为所需的最长暂停时间设置目标值。默认值是 200 毫秒。这个数值是一个软目标，也就是说JVM会尽一切能力满足这个暂停要求，但是不能保证每次暂停一定在这个要求之内。

  根据测试发现，如果我们将这个值设定成50毫秒或者更低的话，JVM为了达到这个要求会将年轻代内存空间设定的非常小，从而导致youngGC的频率大大增高。所以我们并不设定这个参数。

  **-XX:InitiatingHeapOccupancyPercent=45**

  设置触发标记周期的 Java 堆占用率阈值。默认占用率是整个 Java 堆的 45%。就是说当使用内存占到堆总大小的45%的时候，G1将开始**并发标记阶段。**为混合GC做准备，这个数值在测试的时候我想让混合GC晚一些处理所以设定成了70%，经过观察发现如果这个数值设定过大会导致JVM无法启动并发标记，直接进行FullGC处理。

  G1的FullGC是单线程，一个22G的对GC完成需要8S的时间，所以这个值在调优的时候写的45%

> 之前查看ignite，12秒回收了71G

# 实践二：查看进程加载的类

> jcmd命令要使用启动目标进程的用户执行

1. 使用dump内存信息到heap.bin文件
   使用命令`jmap -dump:live,format=b,file=heap.bin pid（进程号）`将进程pid的堆栈信息输出到heap.bin文件中

2. 使用jhat 对heap.bin 文件进行分析
   命令`jhat -J-mx512m heap.bin`， 如果解析过程中出现内存不足，需要加大内存如:`jhat -J-mx800m heap.bin`
3. 通过浏览器访问 `http://ip:7000/`即可看到分析结果。点击每个类，可以查看详细信息，包括该类是被哪个类加载器加载。