---
title: flink on yarn
date: 2022-01-04 00:27:40
tags: point
categories: flink
---

# Flink on YARN模式
在这种模式下Flink的资源由YARN来进行管理，Flink服务被提交到YARN的ResourceManager后，YARN的NodeManager会为Flink生成对应的容器，Flink再将JobManager和TaskManager实例部署到容器中。在这种情况下Flink可以通过JobManager所需要的slots数量来动态的调整TaskManager的资源，达到了资源的可拓展性。Flink官方也推荐正式的生产环境使用这种部署模式。
在YARN上，又分为三种部署模式：
## Session Mode
共享JobManager和TaskManager，所有提交的任务都在一个集群中运行，集群的生命周期独立于任务，任务的开始、结束不影响集群的生命周期。类似于上面的Standalone-cluster模式，任务与任务之间不隔离，共享同一套资源。
## Per-Job Mode
为每个任务创建单独的JobManager和TaskManager集群，每个任务之间互相隔离互不干扰，集群的生命周期随着任务的生命周期结束而结束。这种模式的优点就是任务独占一个集群，资源的隔离性好。
## Application Mode
一个Application可以存在多个任务，这时YARN为每个Application创建集群，Application中的任务共享该集群，资源的隔离是Application级别的，集群的生命周期随着Application的生命周期结束。这种模式更像是Session Mode和Pre-Job Mode的折中方案，既做到了资源的隔离，又提高了任务之间资源的利用率。



## interaction

![](/images/flinkOnYarn/flink_on_yarn.png) 

## two way to submit job on yarn

![](/images/flinkOnYarn/submit_job.png)

### first way：yarn session

> (Start a long-running Flink cluster on YARN)这种方式需要先启动集群，然后在提交作业，接着会向yarn申请一块空间后，资源永远保持不变。如果资源满了，下一个作业就无法提交，只能等到yarn中的其中一个作业执行完成后，释放了资源，那下一个作业才会正常提交.
> 
> ps:所有作业共享Dispatcher和ResourceManager；共享资源；适合规模小执行时间短的作业.适用于本地测试或者开发

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

[Flink-On-Yarn的部署模式](https://blog.csdn.net/u013411339/article/details/95421500?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164455981216780357293300%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=164455981216780357293300&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-21-95421500.nonecase&utm_term=%E2%80%9Cflink+cep%E2%80%9D&spm=1018.2226.3001.4450)
