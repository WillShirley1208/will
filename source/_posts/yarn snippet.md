---
title: yarn point
date: 2021-12-31 02:01:13
tags: point
categories: yarn
---

**Yet Another Resource Negotiator**

YARN 看做一个云操作系统，它负责为应用程序启 动 ApplicationMaster（相当于主线程），然后再由 ApplicationMaster 负责数据切分、任务分配、 启动和监控等工作，而由 ApplicationMaster 启动的各个 Task（相当于子线程）仅负责自己的计 算任务。当所有任务计算完成后，ApplicationMaster 认为应用程序运行完成，然后退出。

![](/images/yarn/yarn_construct.gif)

### 概念

#### contrainer

> 容器（Container）这个东西是 Yarn 对资源做的一层抽象。就像我们平时开发过程中，经常需要对底层一些东西进行封装，只提供给上层一个调用接口一样，Yarn 对资源的管理也是用到了这种思想。

![](/images/yarn/contrainer.jpeg)

> Yarn 将CPU核数，内存这些计算资源都封装成为一个个的容器（Container）。    

- 容器由 NodeManager 启动和管理，并被它所监控。
- 容器被 ResourceManager 进行调度。

#### ResourceManager

> 负责资源管理的，整个系统有且只有一个 RM ，来负责资源的调度。它也包含了两个主要的组件：定时调用器(Scheduler)以及应用管理器(ApplicationManager)。

1. 定时调度器(Scheduler)：从本质上来说，定时调度器就是一种策略，或者说一种算法。当 Client 提交一个任务的时候，它会根据所需要的资源以及当前集群的资源状况进行分配。注意，它只负责向应用程序分配资源，并不做监控以及应用程序的状态跟踪。
2. 应用管理器(ApplicationManager)：同样，听名字就能大概知道它是干嘛的。应用管理器就是负责管理 Client 用户提交的应用。上面不是说到定时调度器（Scheduler）不对用户提交的程序监控嘛，其实啊，监控应用的工作正是由应用管理器（ApplicationManager）完成的。





#### ApplicationMaster

> 每当 Client 提交一个 Application 时候，就会新建一个 ApplicationMaster 。由这个 ApplicationMaster 去与 ResourceManager 申请容器资源，获得资源后会将要运行的程序发送到容器上启动，然后进行分布式计算。
> 
> ps: 大数据分布式计算的思想，大数据难以移动（海量数据移动成本太大，时间太长），那就把容易移动的应用程序发布到各个节点进行计算。





#### NodeManager

> NodeManager 是 ResourceManager 在每台机器的上代理，负责容器的管理，并监控他们的资源使用情况（cpu，内存，磁盘及网络等），以及向 ResourceManager/Scheduler 提供这些资源使用报告。





## submit application to yarn

![](/images/yarn/submit_app_flow.jpeg)

1. Client 向 Yarn 提交 Application，这里我们假设是一个 MapReduce 作业。
2. ResourceManager 向 NodeManager 通信，为该 Application 分配第一个容器。并在这个容器中运行这个应用程序对应的 ApplicationMaster。
3. ApplicationMaster 启动以后，对 作业（也就是 Application） 进行拆分，拆分 task 出来，这些 task 可以运行在一个或多个容器中。然后向 ResourceManager 申请要运行程序的容器，并定时向 ResourceManager 发送心跳。
4. 申请到容器后，ApplicationMaster 会去和容器对应的 NodeManager 通信，而后将作业分发到对应的 NodeManager 中的容器去运行，这里会将拆分后的 MapReduce 进行分发，对应容器中运行的可能是 Map 任务，也可能是 Reduce 任务。
5. 容器中运行的任务会向 ApplicationMaster 发送心跳，汇报自身情况。当程序运行完成后， ApplicationMaster 再向 ResourceManager 注销并释放容器资源。







---

reference:

https://zhuanlan.zhihu.com/p/54192454