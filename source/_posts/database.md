---
title: database
date: 2021-12-29 19:11:30
tags: point
categories: database
---

# hbase

| 节点                   | 端口号 | 协议                    | 使用                                | 说明                    |
| ---------------------- | ------ | ----------------------- | ----------------------------------- | ----------------------- |
| zookeeper              | 2181   |                         | zkCli.sh -server zookeeper1:2181    | 客户端接入              |
| 2888,3888              |        | N/A                     | 集群内部通讯                        |                         |
| HDFS Namenode          | 9000   | HDFS                    | hdfs dfs -ls hdfs://namenode1:9000/ | 客户端接入              |
| 50070                  | HTTP   | http://namenode1:50070/ | 集群监控                            |                         |
| HDFS SecondaryNamenode | 50090  | HTTP                    | http://namenode1:50090/             | secondary监控           |
| HDFS Datanode          | 50010  |                         | N/A                                 | 客户端接入/其他节点接入 |
| 50020                  |        | N/A                     |                                     |                         |
| 50075                  | HTTP   | http://datanode1:50075/ | 节点监控                            |                         |
| HBase Master           | 16000  |                         | hbase-client-1.x.x.jar              | RegionServer接入        |
| 16010                  | HTTP   | http://namenode1:16010/ | 集群监控                            |                         |
| HBase RegionServer     | 16020  |                         | N/A                                 | 客户端接入              |
| 16030                  | HTTP   | http://datanode1:16030/ | 节点监控                            |                         |

# neo4j

## tips

- cypher语法对大小写敏感，注意属性的大小写
- 导入csv出现乱码时，需要通过记事本更改编码格式，如果文本过大，需要一段一段的复制粘贴到新文本中，且以UTF-8格式编码
- MERGE命令是CREATE命令和MATCH命令的组合。`MERGE = CREATE + MATCH`

## 问题

1.
​    **现象**： 在外部处理csv数据，导入服务器，然后load csv到neo4j中，可能由于文件所属问题，不能成功导入csv，提示`Couldn't load the external resource`的错误。
​    **解决**：复制可以导入的csv文件为副本，把待导入的数据粘贴到副本中，load csv副本即可

2.
​    **现象**： 在外部处理csv数据，进行merge操作的时候，提示`cannot merge using null property value for uid`的错误。
​    **解决**：经发现，是由于指令中的属性拼写错误，导致`Neo.ClientError.Statement.SemanticError`的错误，例如把`sfzh`拼写成`sfhm`。

3.
​    **现象**： 大量数据的导入问题。
​    **解决**：需使用[https://neo4j.com/docs/operations-manual/current/tools/import/](https://neo4j.com/docs/operations-manual/current/tools/import/ "大量数据的导入")
​    **注意**：`Import tool is only for newly created databases.`
​        `You can use LOAD CSV cypher command to load into an existing database.`也就是说neo4j-admin import只会在新建数据库的时候会用到

# clickhouse

## 存储架构

> Clickhouse 存储中的最小单位是 DataPart，写入链路为了提升吞吐，放弃了部分写入实时可见性，即数据攒批写入，一次批量写入的数据会落盘成一个 DataPart.
> 
> 它不像 Druid 那样一条一条实时摄入。但 ClickHouse 把数据延迟攒批写入的工作交给来客户端实现，比如达到 10 条记录或每过 5s 间隔写入，换句话说就是可以在用户侧平衡吞吐量和时延，如果在业务高峰期流量不是太大，可以结合实际场景将参数调小，以达到极致的实时效果。

## 查询架构

### 计算能力方面

Clickhouse 采用向量化函数和 aggregator 算子极大地提升了聚合计算性能，配合完备的 SQL 能力使得数据分析变得更加简单、灵活。

### 数据扫描方面

ClickHouse 是完全列式的存储计算引擎，而且是以有序存储为核心，在查询扫描数据的过程中，首先会根据存储的有序性、列存块统计信息、分区键等信息推断出需要扫描的列存块，然后进行并行的数据扫描，像表达式计算、聚合算子都是在正规的计算引擎中处理。从计算引擎到数据扫描，数据流转都是以列存块为单位，高度向量化的。

### 高并发服务方面

Clickhouse 的并发能力其实是与并行计算量和机器资源决定的。如果查询需要扫描的数据量和计算复杂度很大，并发度就会降低，但是如果保证单个 query 的 latency 足够低（增加内存和 cpu 资源），部分场景下用户可以通过设置合适的系统参数来提升并发能力，比如 max_threads 等。其他分析型系统（例如 Elasticsearch）的并发能力为什么很好，从 Cache 设计层面来看，ES 的 Cache 包括 Query Cache, Request Cache，Data Cache，Index Cache，从查询结果到索引扫描结果层层的 Cache 加速，因为 Elasticsearch 认为它的场景下存在热点数据，可能被反复查询。反观 ClickHouse，只有一个面向 IO 的 UnCompressedBlockCache 和系统的 PageCache，为了实现更优秀的并发，我们很容易想到在 Clickhouse 外面加一层 Cache，比如 redis，但是分析场景下的数据和查询都是多变的，查询结果等 Cache 都不容易命中，而且在广投业务中实时查询的数据是基于 T 之后不断更新的数据，如果外挂缓存将降低数据查询的时效性。

## 技巧

### 唯一键约束

```sql
CREATE TABLE IF NOT EXISTS qilu.t_01(
	C1 String,
	C2 String,
	C3 String,
	C4 Date,
	PRIMARY KEY (C1) # 要设置主键
) engine=ReplacingMergeTree() # 引擎要用ReplacingMergeTree
 ORDER BY C1; # 要设置排序

optimize table t_01 FINAL; # 要强制合并分区
```

# rqlite

## build

```shell
cd $GOPATH/src/github.com/rqlite/rqlite
go install ./...  # install in $GOPATH/bin/ directory
```



## deploy cluster

```shell
# node 1
nohup rqlited -node-id 1 -http-addr host1:4001 -raft-addr host1:4002 ~/rqlite/node > rqlite.log 2>&1 &
# e.g. rqlited -node-id 1 -http-addr 172.20.7.232:4001 -raft-addr 172.20.7.232:4002 ~/rqlite/node

# enter shell with host1 ip
rqlite -H x.x.x.x -p 4001 

# check health
curl http://x.x.x.x:4001/status | jq .
curl http://x.x.x.x:4001/status?pretty

# join cluster
# node 2
rqlited -node-id 2 -http-addr host2:4001 -raft-addr host2:4002 -join host1:4002 ~/node
# e.g. nohup rqlited -node-id 2 -http-addr 172.20.7.233:14001 -raft-addr 172.20.7.233:14002 -join 172.20.7.232:14002 ~/rqlite/node > rqlite.log 2>&1 &
# node 3
rqlited -node-id 3 -http-addr host3:4001 -raft-addr host3:4002 -join host1:4002 ~/node
# e.g. nohup rqlited -node-id 3 -http-addr 172.20.7.234:14001 -raft-addr 172.20.7.234:14002 -join 172.20.7.232:14002 ~/rqlite/node > rqlite.log 2>&1 &
```

## shell

```shell
.nodes
172.20.7.232:14001> .nodes
1:
  leader: false
  time: 2.63e-7
  time_s: 433ns
  id: 1
  api_addr: http://172.20.7.232:14001
  addr: 172.20.7.232:14002
  voter: true
  reachable: true
2:
  time_s: 409ns
  id: 2
  api_addr: http://172.20.7.233:14001
  addr: 172.20.7.233:14002
  voter: true
  reachable: true
  leader: true
  time: 2.34e-7
3:
  api_addr: http://172.20.7.234:14001
  addr: 172.20.7.234:14002
  voter: true
  reachable: true
  leader: false
  time: 1.91e-7
  time_s: 354ns
  id: 3
```



# postgreSQL



### 终端登录pg

- 如果之前没有登录过，需要设置当前用户进行登录操作

  There is no default username and password without you creating one. The simplest possible setup is to follow these steps to set up your own user as a superuser.

  At a terminal prompt, create a postgres user with your own username

  ```
  sudo -u postgres createuser --superuser $USER	# $USER无须替换
  ```

  Start the postgresql command prompt as your username but running as root since you didn't set a password yet;

  ```
  sudo -u postgres psql
  ```

  At the postgresql prompt, set your password;

  ```
  \password $USER    # 其中$USER需要替换成当前用户名
  ```

  After that, you should be able to log on just fine.



- 如果之前设置了上面的步骤，可直接运行

  ```、
  psql postgres
  ```


### 导入文件

```shell
psql postgres	# login command
\c some_database	# choose database
\i \path\TO\file_name.sql	# execute sql
```

### 连接指定schema

- 如果不指定schema的话，会默认访问public的schema

- 指定schema

  `jdbc:postgresql://localhost:5432/mydatabase?currentSchema=myschema`

# starrocks

## 安装部署

## 注意项

- 安装前配置环境变量

  ```shell
  export STARROCKS_HOME=xxx
  ```

- 启动mysql客户端

  ```shell
  mysql -h <fe_ip> -P<fe_query_port> -uroot -p # 密码为空，直接回车即可
  ```


-------------------------------------------

## FE

```
netstat -tunlp | grep 18030
netstat -tunlp | grep 19010
netstat -tunlp | grep 19020
netstat -tunlp | grep 19030

http_port = 18030
rpc_port = 19020
query_port = 19030
edit_log_port = 19010
```

- 启动FE
  `start_fe.sh --daemon`

- 查看FE状态+
  `SHOW PROC '/frontends'\G`

- 添加新FE节点（follow和observer)
  `./start_fe.sh --helper 172.18.244.74:19010 --daemon`

  注意：

  - 在mysql command先将实例添加进集群，然后逐个启动实例。（只在首次新增节点时操作）

  - 各个节点的时间一定要同步，不然FE的心跳超过5s时差，就会报错

- 扩容FE
  `ALTER SYSTEM ADD FOLLOWER 'ip:19010';`
  `ALTER SYSTEM ADD observer 'ip:19010';`

- 缩容FE
  `ALTER SYSTEM DROP follower "ip:19010";`
  `ALTER SYSTEM DROP observer "ip:19010";`

--------------------------------------------

## BE

```
netstat -tunlp | grep 19060
netstat -tunlp | grep 18040
netstat -tunlp | grep 19050
netstat -tunlp | grep 18060

be_port = 19060
be_http_port = 18040
heartbeat_service_port = 19050
brpc_port = 18060
```

- 查看BE状态
  `SHOW PROC '/backends'\G`

- 启动BE 
  `./start_be.sh --daemon`

- 添加BE节点
  `ALTER SYSTEM ADD BACKEND "172.18.244.74:19050";`

- 缩容BE节点
  `ALTER SYSTEM DECOMMISSION BACKEND "172.18.244.74:19050";`
  等待该节点BE从列表中消失（查看命令SHOW PROC '/backends';），即可使用./stop_be.sh停掉服务

- 崩溃排查日志`be.out`




-------------------------------------------

## 配置修改

- FE修改端口信息，需要清理meta

```
1. 重建meta目录
2. 重启主节点FE
3. 登录mysql command，添加新FE节点（follow和observer），添加BE节点
4. 启动follower和observer节点
```

- BE修改端口信息，需要清理storage

```
1. 重建storage目录（是否可以修改cluster_id进行集群处理）
2. 逐个启动BE服务
3. 登录mysql command，添加新BE节点（ALTER SYSTEM ADD BACKEND "ip:19050";）
```

- 修改密码
  `SET PASSWORD = PASSWORD('datacanvas')`

## TIPS

- 节点时间需要同步（延迟需要在5秒内），不然starrocks无法扩容FE节点

# tools

## logstash

- Logstash Forwarder/Beats负责在源服务器上收集日志数据，然后将数据发送到集中式的Logstash或Kafka服务器。

- Logstash是一个灵活的数据收集引擎，它可以从多个来源（包括日志文件、标准输入、Beats等）采集数据，进行转换、过滤和解析，最后将数据发送到各种目的地，比如Elasticsearch、Kafka等。在日志处理中，Logstash常被用作数据的转换和预处理工具。

# mybatis

## 知识点

- about date jdbcType

  ```
  #{beginDate,jdbcType=DATE} (no time of the day), or
  #{beginDate,jdbcType=TIMESTAMP} (if you need to include the time of the day).
  ```

And the Java parameter you want to apply should be of type:

  ```
java.sql.Date (date without time)
java.sql.Timestamp (timestamp)
java.util.Date (date and time).
  ```

- the Greater Than/Equal Operator and the Less Than/Equal Operators:

  ```
  ROWNUM &gt;= 20
  ROWNUM &lt;= 20
  ```

## 问题解决

使用SpringMVC+Mybatis框架写项目的时候，在mapper里面的sql语句

```xml
	<if test="updateTime != null">
		and update_time &gt; #{updateTime,jdbcType=TIMESTAMP}
	</if>
```

出错：`There is no getter for property named 'updateTime' in 'class java.util.Date'`

原因是：
如果将and语句中的#｛属性｝写在if语句中，mybatis会去对象中从它的get方法中取值，而我之前是直接传递的值，并没有封装对象。
解决办法：

 	1. 把updateTime需改为date
 	2. 把updateTime代表的参数封装到一个对象中

