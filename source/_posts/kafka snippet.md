---
title: kafka learn
date: 2019-06-11 14:01:30
tags: learn
categories: kafka
---

## port（confluent reference）

| Component                         | Default Port |
|:--------------------------------- |:------------ |
| Zookeeper                         | 2181         |
| Apache Kafka brokers (plain text) | 9092         |
| Schema Registry REST API          | 8081         |
| REST Proxy                        | 8082         |
| Kafka Connect REST API            | 8083         |
| Confluent Control Center          | 9021         |

## System Architecture

![](https://i.imgur.com/gG71h6m.png)

- **kafka Producer API:** Applications directly producing data (ex: clickstream, logs, IoT).
- **Kafka Connect Source API:** Applications bridging between a datastore we don’t control and Kafka (ex: CDC, Postgres, MongoDB, Twitter, REST API).
- **Kafka Streams API / KSQL:** Applications wanting to consume from Kafka and produce back into Kafka, also called stream processing. Use KSQL if you think you can write your real-time job as SQL-like, use Kafka Streams API if you think you’re going to need to write complex logic for your job.
- **Kafka Consumer API:** Read a stream and perform real-time actions on it (e.g. send email…)
- **Kafka Connect Sink API**: Read a stream and store it into a target store (ex: Kafka to S3, Kafka to HDFS, Kafka to PostgreSQL, Kafka to MongoDB, etc.)

![](https://www.sohamkamani.com/basic_arch-e0b11d2ab5b62a78cb7fd26d3a9d279b.svg)

1. **Zookeeper** : Which is used by Kafka to maintain state between the nodes of the cluster.
2. **Kafka brokers** : The “pipes” in our pipeline, which store and emit data.
3. **Producers** : That insert data into the cluster.
4. **Consumers** : That read data from the cluster.

![](https://kafka.apache.org/22/images/kafka-apis.png)

### Creating a topic

```shell
bin/kafka-topics.sh \
--create \
--topic my-kafka-topic \
--zookeeper localhost:2181 \
--partitions 3 \
--replication-factor 2
```

- The `paritions` options lets you decide how many brokers you want your data to be split between. Since we set up 3 brokers, we can set this option to 3.
- The `replication-factor` describes how many copies of you data you want (in case one of the brokers goes down, you still have your data on the others).

### Producer

```shell
bin/kafka-console-producer.sh \
--broker-list localhost:9093,localhost:9094,localhost:9095 \
--topic my-kafka-topic
```

### Consumers

```shell
bin/kafka-console-consumer.sh \
--bootstrap-server localhost:9093 \
--topic my-kafka-topic \
--from-beginning
```

- The `bootstrap-server` can be any one of the brokers in the cluster.
- The `from-beginning` option tells the cluster that you want all the messages that it currently has with it, even messages that we put into it previously.

Try starting another consumer in a different terminal window:

```shell
bin/kafka-console-consumer.sh \
--bootstrap-server localhost:9093 \
--topic my-kafka-topic \
--from-beginning \
--group group2
```

## Kafka as a Messaging System

- As with a queue the consumer group allows you to divide up processing over a collection of processes (the members of the consumer group). As with publish-subscribe, Kafka allows you to broadcast messages to multiple consumer groups.
  
  ![](https://kafka.apache.org/22/images/consumer-groups.png)

- Kafka does it better. By having a notion of parallelism—the partition—within the topics, Kafka is able to provide both ordering guarantees and load balancing over a pool of consumer processes. This is achieved by assigning the partitions in the topic to the consumers in the consumer group so that each partition is consumed by exactly one consumer in the group. By doing this we ensure that the consumer is the only reader of that partition and consumes the data in order. Since there are many partitions this still balances the load over many consumer instances. Note however that there cannot be more consumer instances in a consumer group than partitions.

- Each record consists of a key, a value, and a timestamp.

## Kafka as a Storage System

## Kafka as Stream Processing

## source code

### 源码文件结构

| 目录             | 作用                                                             |
| -------------- | -------------------------------------------------------------- |
| bin            | 存放可直接在Linux或Windows上运行的.sh文件和.bat文件，包含Kafka常用操作以及ZooKeeper便捷脚本 |
| checkstyle     | 存放代码规范检查文档                                                     |
| clients        | 客户端的实现                                                         |
| config         | 存放配置文件                                                         |
| connetct       | Kafka Connect工具的实现                                             |
| core           | 核心模块                                                           |
| docs           | 官方文档                                                           |
| examples       | Kafka生产者消费者简单Demo                                              |
| jmh-benchmarks | 基准测试模块                                                         |
| log4j-appender | 日志模块                                                           |
| streams        | Kafka Streams客户端库                                              |
| tools          | 工具类                                                            |

### 核心模块结构

| 目录          | 作用                                                |
| ----------- | ------------------------------------------------- |
| admin       | 管理模块，操作和管理topic， broker, consumer group， records等 |
| api         | 封装调用                                              |
| client      | Producer生产的元数据信息的传递                               |
| cluster     | 存活的Broker集群、分区、副本以及他们的底层属性和相互关系                   |
| common      | 异常类、枚举类、格式化类、配置类等                                 |
| consumer    | 旧版本的废弃消费者类                                        |
| controller  | Kafka集群控制中心的选举，分区状态管理，分区副本状态管理，监听ZooKeeper数据变化等   |
| coordinator | GroupCoordinator处理一般组成员资格和偏移量。transaction管理事务     |
| javaapi     | 给java调用的生产者、消费者、消息集api                            |
| log         | 管理log，它是消息存储的形式，可对应到磁盘上的一个文件夹                     |
| message     | 由消息封装而成的一个压缩消息集                                   |
| metrics     | Kafka监控模块                                         |
| network     | 网络管理模块，对客户端连接的处理                                  |
| producer    | 旧版本的废弃生产者类                                        |
| security    | 权限管理                                              |
| serializer  | 消息序列化与反序列化处理                                      |
| server      | 服务器端的实现                                           |
| tools       | 各种控制台工具的实现                                        |
| utils       | 工具类                                               |
| zk          | 提供与ZooKeeper交互的管理方法和在管道之上的更高级别的Kafka特定操作          |
| zookeeper   | 一个促进管道传输请求的ZooKeeper客户端                           |

# 主题分区

- 调整分区

```shell
kafka-topics --zookeeper localhost:2181 --alter --topic my-topic --partitions 10
```

- 查看分区

```shell
kafka-topics --zookeeper localhost:2181 --describe --topic my-topic
```



# Restful API

## AVRO主题相关

> 一般schema registry 默认端口是8081（有时候避免与flink-jobmanager端口冲突，改为别的端口号 8091）

- 获取所有主题信息

  ```shell
  curl -X GET "http://<schema-registry-url>:<port>/subjects"
  ```

- 根据schema id查下相关信息

  ```shell
  curl -X GET "http://<schema-registry-url>:<port>/schemas/ids/<schema-id>"
  ```

- 获取指定主题key/value相关schema信息

  ```shell
  GET http://<schema-registry-url>:<port>/subjects/<topic-name>-value/versions/latest
  ```

  or

  ```shell
  GET http://<schema-registry-url>:<port>/subjects/<topic-name>-key/versions/latest
  ```

  

# 参数调优

# producer

- batch.size 默认16384
- request.time.ms 默认30000（30秒）
