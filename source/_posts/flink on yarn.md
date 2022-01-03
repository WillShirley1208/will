---
title: flink on yarn
date: 2022-01-04 00:27:40
tags: point
categories: flink
---

## interaction

![](/images/flinkOnYarn/flink_on_yarn.png) 

> 

## two way to submit job on yarn

![](/images/flinkOnYarn/submit_job.png)

### first way：yarn session

> (Start a long-running Flink cluster on YARN)这种方式需要先启动集群，然后在提交作业，接着会向yarn申请一块空间后，资源永远保持不变。如果资源满了，下一个作业就无法提交，只能等到yarn中的其中一个作业执行完成后，释放了资源，那下一个作业才会正常提交.
> 
> ps:适用于本地测试或者开发



#### mode one: 客户端模式

> 可以启动多个yarn session，一个yarn session模式对应一个JobManager,并按照需求提交作业，同一个Session中可以提交多个Flink作业。如果想要停止Flink Yarn Application，需要通过yarn application -kill命令来停止.

```shell
bin/yarn-session.sh -n 2 -jm 1024 -tm 4096 -s 6
```

- YarnSessionClusterEntrypoint进程
  
  代表本节点可以命令方式提交job，而且可以不用指定-m参数。
  
  - 本节点提交任务
    
    `bin/flink run ~/flink-demo-wordcount.jar`
  
  - 如果需要在其他主机节点提交任务
    
    `bin/flink run -m vmhome10.com:43258 examples/batch/WordCount.jar`

- FlinkYarnSessionCli进程
  
  代表yarn-session集群入口，实际就是jobmanager节点，也是yarn的ApplicationMaster节点。

#### mode two: 分离式模式

> JobManager的个数只能是一个，同一个Session中可以提交多个Flink作业。如果想要停止Flink Yarn Application，需要通过yarn application -kill命令来停止。通过-d指定分离模式.

```shell
./bin/yarn-session.sh -nm test3 -d
```

> 在所有的节点只会出现一个 YarnSessionClusterEntrypoint进程





### second way: flink run

> 直接在YARN上提交运行Flink作业(Run a Flink job on YARN)，这种方式的好处是一个任务会对应一个job,即没提交一个作业会根据自身的情况，向yarn申请资源，直到作业执行完成，并不会影响下一个作业的正常运行，除非是yarn上面没有任何资源的情况下。
> 
> ps:适用于生产环境，可启动多个yarn session （bin/yarn-session.sh -nm ipOrHostName）

```shell
./bin/flink run -m addressOfJobmanager -yn 1 -yjm 1024 -ytm 1024 ./examples/batch/WordCount.jar
```

注意使用参数-m yarn-cluster提交到yarn集群。

- 运行到指定的yarn session
  
  可以指定 -yid,--yarnapplicationId <arg> Attach to running YARN session来附加到到特定的yarn session上运行



---

reference

[Flink on yarn部署模式 - 简书](https://www.jianshu.com/p/1b05202c4fb6)

[flink on yarn模式下两种提交job方式 - 我是属车的 - 博客园](https://www.cnblogs.com/asker009/p/11327533.html)
