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

  ## 动态配置

### 方案一: nacos config配置中心获取

- 通过配置nacos config和 controller获取

  ```properties
  config:
    enabled: true
    server-addr: ${spring.cloud.nacos.server-addr}
    file-extension: yaml
    namespace: public
  ```

  controller类使用 @Refresh，刷新动态配置

### 方案二：nacos client 监听指定配置文件（recommended）

- 编写监听类
- 手动刷新配置



### reference 

- https://www.lijunyi.xyz/docs/SpringCloud/SpringCloud.html#%E6%95%B4%E4%BD%93%E5%A4%A7%E7%BA%B2%F0%9F%92%A8
- http://47.109.59.205/2023/01/02/SpringCloud%E5%AE%9E%E7%94%A8%E7%AF%8702/#1-Nacos%E9%85%8D%E7%BD%AE%E7%AE%A1%E7%90%86
- https://cloud.tencent.com/developer/article/2096938
- https://www.cnblogs.com/taojietaoge/p/16638226.html