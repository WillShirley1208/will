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

  

- 启动FE时

  - 启动FE为LEADER节点

    ```
    ./fe/bin/start_fe.sh --daemon
    ```

    

- 添加新FE

  - 在mysql command先将实例添加进集群，然后逐个启动实例

  - 各个节点的时间一定要同步，不然FE的心跳超过5s时差，就会报错

    