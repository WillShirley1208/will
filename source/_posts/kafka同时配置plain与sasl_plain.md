---
title: kafka plain & sasl_plain
date: 2023-09-11 19:57:52
tags: config
categories: kafka
---



> 此方案的缺点，在sasl_plaintext模式下，不能动态创建用户，或修改用户账号信息

# 服务端

## 第一步：准备kafka_server_jaas.conf文件

```conf
KafkaServer {
    org.apache.kafka.common.security.plain.PlainLoginModule required
    username="admin"
    password="datacanvas"
    user_admin="datacanvas"
    user_qlb="qlbrtdsp";
};
```



## 第二步：设置kafka-server-start.sh

```sh
export KAFKA_OPTS="-Djava.security.auth.login.config=/home/sasl/kafka_2.13-3.3.2/config/kafka_server_jaas.conf"
```

- 注意修改路径

## 第三步：设置server.properties

```properties
listeners=PLAINTEXT://0.0.0.0:9092,SASL_PLAINTEXT://0.0.0.0:29092
advertised.listeners=PLAINTEXT://172.20.58.93:9092,SASL_PLAINTEXT://172.20.58.93:29092
security.inter.broker.protocol=SASL_PLAINTEXT
sasl.mechanism.inter.broker.protocol=PLAIN
sasl.enabled.mechanisms=PLAIN
```

- listeners设置了两种协议 明文连接PLAINTEXT 和 安全连接 SASL_PLAINTEXT
  - `0.0.0.0` 表示 Kafka Broker 将监听所有可用的网络接口，这意味着它将接受来自任何 IP 地址的连接请求。

- 注意zookeeer存储的位置 (/brokers)

- 设置默认副本数 default.replication.factor=3 和 num.partitions=1

- 测试使用`log.dirs=/home/sasl/data/kafka-logs`

```properties
# authorizer.class.name=kafka.security.auth.SimpleAclAuthorizer 旧版本配置
authorizer.class.name=kafka.security.authorizer.AclAuthorizer
super.users=User:admin
allow.everyone.if.no.acl.found=false
```



## 第四步：设置acl-config.properties

在config目录新增acl-config.properties设置 admin信息

```properties
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="admin" password="datacanvas";
security.protocol=SASL_PLAINTEXT
sasl.mechanism=PLAIN
```



## 第五步：启动kafka server 

```properties
bin/kafka-server-start.sh -daemon ./config/server.properties
```



## 第六步：配置ACL授权

- 授权bigdata用户可以访问主题前缀为ODS的数据，且限制消费组 GROUP-BIGDATA

  ```
  bin/kafka-acls.sh --bootstrap-server 172.20.58.93:29092 --command-config /home/sasl/kafka_2.13-3.3.2/config/acl-config.properties --add --allow-principal User:bigdata --operation Read --topic ODS* --group GROUP-BIGDATA --group 'GROUP-BIGDATA'
  ```

  

---

# 客户端

- PLAINTEXT连接保持原先操作

- SASL_PLAINTEXT

  连接配置添加用户登录信息

  ```shell
  org.apache.kafka.common.security.plain.PlainLoginModule required username="admin" password="datacanvas";
  ```
