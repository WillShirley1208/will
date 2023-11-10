---
title: nacos snippet
date: 2021-12-01 15:14:40
tags: snippet
categories: spring cloud
---

### 启动

- 2.x版本启动，需要添加 -m属性
  
  `./startup.sh -m standalone`

## 心跳机制



### 健康检查

服务端接受到客户端的服务注册请求后，在创建空的Service后，就会开启健康检查任务

- 在超过15秒没收到客户端心跳时，就会把注册表中实例的健康状态改为false
- 超时30秒没有收到客户端心跳时，就会从注册表表剔除该实例，会使用HTTP DELETE方式调用/v1/ns/instance地址

## open API

- 获取心跳

```shell
curl -X PUT '127.0.0.1:8848/nacos/v2/ns/instance/beat' \
-d '{
      "namespaceId": "jarvex_space",
      "serviceName": "jarvex-common-group@@entity-server",
      "ip": "127.0.0.1",
      "port": "5333"
}'
或
curl -X PUT '127.0.0.1:8848/nacos/v2/ns/instance/beat' \
      -d 'namespaceId=jarvex_space' \
      -d 'serviceName=jarvex-common-group@@entity-server' \
      -d 'ip=127.0.0.1' \
      -d 'port=5333'
```

- 创建命令空间
```shell
curl -d 'namespaceId=jarvex_space' \
  -d 'namespaceName=jarvex' \
  -X POS
```

- 删除持久化实例

```shell
curl -X DELETE "http://127.0.0.1:8848/nacos/v2/ns/instance?serviceName=jarvex-gateway&ip=192.168.1.148&port=8085&namespaceId=jarvex_space&groupName=jarvex-common-group&ephemeral=false"
```

  