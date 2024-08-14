---
title: kafka plain & sasl_scram
date: 2023-09-11 19:58:47
tags: config
categories: kafka
---



> 已验证 kafka 2.3.1
>
> 此方案可以动态创建用户，或修改用户账号信息

> SASL（Simple Authentication and Security Layer）
>
> 参考 https://kafka.apache.org/documentation/#security_sasl_scram

# 服务端

> 环境 zookeeper端口22181 ，kafka broker端口39092
>
> 无需重启zookeeper

## 第一步：创建 SCRAM 证书

> 在broker启动之前

- 创建admin用户证书

  启动之前（必须）

  ```shell
  bin/kafka-configs.sh --zookeeper 172.20.58.93:22181 --alter --add-config 'SCRAM-SHA-256=[password=datacanvas],SCRAM-SHA-512=[password=datacanvas]' --entity-type users --entity-name admin
  ```

  > 会在 zookeeper生产目录 config，上面zookeeper参数值与kafka server.properties的zookeeper connect配置一致，也和offsetExplorer的chroot path一致
  
- 列出用户已有证书

  ```shell
  bin/kafka-configs.sh --zookeeper 172.20.58.93:22181 --describe --entity-type users --entity-name alice
  ```

- 删除用户证书

  ```shell
  bin/kafka-configs.sh --zookeeper 172.20.58.93:22181 --alter --delete-config 'SCRAM-SHA-512' --entity-type users --entity-name alice
  ```
- 创建普通用户alice证书

  > 可在启动之前或启动之后皆可
  
  ```shell
  bin/kafka-configs.sh --zookeeper 172.20.58.93:22181 --alter --add-config 'SCRAM-SHA-256=[iterations=8192,password=alice-secret]' --entity-type users --entity-name alice
  ```



## 第一步：准备kafka_server_jaas.conf文件

```conf
KafkaServer {
    org.apache.kafka.common.security.scram.ScramLoginModule required
    username="admin"
    password="datacanvas";
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
sasl.mechanism.inter.broker.protocol=SCRAM-SHA-256
sasl.enabled.mechanisms=SCRAM-SHA-256
```

- listeners设置了两种协议 明文连接PLAINTEXT 和 安全连接 SASL_SSL
  - `0.0.0.0` 表示 Kafka Broker 将监听所有可用的网络接口，这意味着它将接受来自任何 IP 地址的连接请求。
- 注意zookeeer存储的位置 (/brokers)
- 设置默认副本数 default.replication.factor=3 和 num.partitions=1
- 测试使用`log.dirs=/home/sasl/data/kafka-logs`

-  ACLs相关配置

```properties
# authorizer.class.name=kafka.security.auth.SimpleAclAuthorizer 旧版本配置
authorizer.class.name=kafka.security.authorizer.AclAuthorizer
# 这里添加ANONYMOUS为超级用户，主要为了listener for plain(如何只用sasl，可以不配置ANONYMOUS)
super.users=User:admin;User:ANONYMOUS 
allow.everyone.if.no.acl.found=false 
```

>  默认为true,默认情况只通过用户密码认证管控用户，acl只会对--deny-principal起效（所以默认同时使用 plain和scram，需要保持默认true。如果单独使用scram，则需要设置为false）

## 第四步：设置acl-config.properties

在config目录新增acl-config.properties设置 admin信息

```properties
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="admin" password="datacanvas";
security.protocol=SASL_PLAINTEXT
sasl.mechanism=SCRAM-SHA-256
```



## 第五步：启动kafka server 

```properties
bin/kafka-server-start.sh -daemon ./config/server.properties
```



## 第六步：配置ACL授权

- 授权bigdata用户可以访问主题前缀为ODS的数据，且限制消费组 GROUP-BIGDATA

  ```shell
  bin/kafka-acls.sh --bootstrap-server 172.20.58.93:29092 --command-config /path/to/config/acl-config.properties --add --allow-principal User:bigdata --operation Read --group GROUP-BIGDATA --topic ODS --resource-pattern-type prefixed
  ```

  - --resource-pattern-type prefixed 指定ODS前缀

- 移除权限

  ```shell
  bin/kafka-acls.sh --bootstrap-server localhost:9092 --command-config /home/sasl/kafka_2.13-3.3.2/config/acl-config.properties --remove --allow-principal User:Bob --allow-principal User:Alice --allow-host 198.51.100.0 --allow-host 198.51.100.1 --operation Read --operation Write --topic Test-topic 
  ```


- 禁止删除指定主题的权限

  ```shell
  bin/kafka-acls.sh --bootstrap-server 172.20.58.93:29092 --command-config /home/sasl/kafka_2.13-3.3.2/config/acl-config.properties --add --deny-principal User:bigdata --operation Write --operation Delete --topic ODS --resource-pattern-type prefixed
  ```

  

---

# 客户端

- PLAIN连接保持原先操作

- SASL_PLAINTEXT

  连接配置添加用户登录信息

  ```shell
  org.apache.kafka.common.security.scram.ScramLoginModule required username="admin" password="datacanvas";
  ```
