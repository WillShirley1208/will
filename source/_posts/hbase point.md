---
title: hbase point
date: 2022-04-01 10:01:30
tags: point
categories: hbase
---

| 节点                     | 端口号   | 协议                      | 使用                                  | 说明             |
| ---------------------- | ----- | ----------------------- | ----------------------------------- | -------------- |
| zookeeper              | 2181  |                         | zkCli.sh -server zookeeper1:2181    | 客户端接入          |
| 2888,3888              |       | N/A                     | 集群内部通讯                              |                |
| HDFS Namenode          | 9000  | HDFS                    | hdfs dfs -ls hdfs://namenode1:9000/ | 客户端接入          |
| 50070                  | HTTP  | http://namenode1:50070/ | 集群监控                                |                |
| HDFS SecondaryNamenode | 50090 | HTTP                    | http://namenode1:50090/             | secondary监控    |
| HDFS Datanode          | 50010 |                         | N/A                                 | 客户端接入/其他节点接入   |
| 50020                  |       | N/A                     |                                     |                |
| 50075                  | HTTP  | http://datanode1:50075/ | 节点监控                                |                |
| HBase Master           | 16000 |                         | hbase-client-1.x.x.jar              | RegionServer接入 |
| 16010                  | HTTP  | http://namenode1:16010/ | 集群监控                                |                |
| HBase RegionServer     | 16020 |                         | N/A                                 | 客户端接入          |
| 16030                  | HTTP  | http://datanode1:16030/ | 节点监控                                |                |
