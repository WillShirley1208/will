---
title: redis snippet
date: 2018-02-16 12:01:30
tags: snippet
categories: redis
---

### 知识点

- show all keys

  `KEYS *`

- Delete All Keys In Redis
  ```bash
  	Delete all keys from all Redis databases:
    `$ redis-cli FLUSHALL`
    Delete all keys of the currently selected Redis database:
    `$ redis-cli FLUSHDB`
    Delete all keys of the specified Redis database:
    `$ redis-cli -n <database_number> FLUSHDB`
  ```

### 问题解决

- RedisConnectionFailureException
```
redis.conf中默认的是bind 127.0.0.1，需要将这段注释
设置protected-mode no，因为如果是yes的话，只能进行本地访问。如果设置了密码和bind这个可以开启，如果没有设置，这个属性可以设置为no即可。
daemonize  yes表示后台运行
```
- 需要安装gcc

- make时，提示fatal error: jemalloc/jemalloc.h: No such file or directory

  ```bash
  解决：   
  cd src/deps
  make hiredis jemalloc linenoise lua geohash-int
  cd ..
  make
  sudo make install
  
  ```

 ### 自定义配置redis server
 涉及到的目录或文件有
 ```
 1.配置文件	/etc/redis/xxxx.conf
 2.日志、数据文件	/data/redis/xxxx
 3.启动脚本	/etc/init.d/redis_xxxx
 
客户端启动时	redis-cli -p xxxx
 ```