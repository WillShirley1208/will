---
title: starrocks point
date: 2023-07-07 14:52:04
tags: point
categories: olap
---

# 安装部署

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