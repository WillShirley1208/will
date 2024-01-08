---
title: SSO (single sign-on)
date: 2023-12-16 10:57:00
tags: learn
categories: sso
---

>  SSO (single sign-on)

> https://ninjaauth.com/sso-single-sign-on



## keycloak

### 前提

- 需要jdk9以上环境



### config server

- 编辑 `/conf/keycloak.conf `，配置mysql连接信息 (mysql创建keycloak库)

- 配置 `admin cli `环境变量 （方便直接命令操作，等同于界面操作）

  ```shell
  # keycloak
  export KCADM="/Users/dongwei/Applications/keycloak-23.0.3/bin/kcadm.sh"
  export HOST_FOR_KCADM=localhost
  ```



### bootstrap

- 首次启动

  `KEYCLOAK_ADMIN=admin KEYCLOAK_ADMIN_PASSWORD=xxxxxx ./bin/kc.sh start-dev`

- 后续启动

  `./bin/kc.sh start-dev`

- 生效 `KCADM`

  `$KCADM config credentials --server http://localhost:8080 --realm master --user admin`

  提示输入密码即可 (1sou2sou)

​		验证 `$KCADM get serverinfo`





### config realm

- 创建新的realm

  `$KCADM create realms -s realm="jarvex" -s enabled=true`



### config clients

```shell
$KCADM create clients -r jarvex -s clientId="jarvex-client-test-1" -s enabled=true -s name="test Client 1" -s protocol=openid-connect -s publicClient=true -s standardFlowEnabled=true -s 'redirectUris=["http://127.0.0.1:8001/app/*"]' -s baseUrl="http://127.0.0.1:8001/app/" -s 'webOrigins=["*"]'
```

