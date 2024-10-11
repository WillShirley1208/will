---
title: rpc vs mq
date: 2018-12-08 23:01:30
tags: vs
categories: a vs b
---

# 系统结构

## rpc

>  Remote Procedure Call，远程过程调用

服务之间的调用大多采用rpc的方式调用，或者消息队列的方式进行解耦。

RPC与http调用的区别
rpc是基于TCP/IP协议的，而HTTP服务主要是基于HTTP协议的，而http协议是在传输层协议TCP之上的。所以rpc效率更好。

RPC是指远程过程调用，也就是说两台服务器A，B，一个应用部署在A服务器上，想要调用B服务器上应用提供的函数/方法，
由于不在一个内存空间，不能直接调用，需要通过网络来表达调用的语义和传达调用的数据。

由于计算能力需要横向扩展，需要在多台机器组成的集群上部署应用。

```
Consumer => Provider
Consumer调用的Provider提供的服务。
```

- 同步调用，对于要等待返回结果/处理结果的场景，RPC是可以非常自然直觉的使用方式。
- 由于等待结果，Consumer（Client）会有线程消耗。
- RPC也可以是异步调用，如果以异步RPC的方式使用，Consumer（Client）线程消耗可以去掉。但不能做到像消息一样暂存消息/请求，压力会直接传导到服务Provider。

## message queue

```
Sender => Queue <= Receiver
Sender发送消息给Queue;Receiver从Queue拿到消息来处理
```

- Message Queue把请求的压力保存一下，逐渐释放出来，让处理者按照自己的节奏来处理。
- Message Queue引入一下新的结点，让系统的可靠性会受Message Queue结点的影响。
- Message Queue是**异步单向**的消息。发送消息设计成是**不需要等待**消息处理的完成。

### view

- 这两者可以拿来比较，但是个人感觉并不是同一个层面的问题。RPC是分布式服务之间调用的一种解决方案，是我们在做架构设计决策时同分布式对象，REST等层面的东西比较，决策的一个方案！ 消息系统更多是我们为了解决系统之间的解耦，以及性能问题等方面所考虑的方案。