---
title: redis snippet
date: 2018-02-16 12:01:30
tags: snippet
categories: redis
---

### 数据类型

- String
  
  ```
  redis 127.0.0.1:6379> SET name "runoob"
  OK
  redis 127.0.0.1:6379> GET name
  "runoob"
  ```

- Hash
  
  ```
  redis> HMSET myhash field1 "Hello" field2 "World"
  "OK"
  redis> HGET myhash field1
  "Hello"
  redis> HGET myhash field2
  "World"
  ```

- List
  
  ```
  redis 127.0.0.1:6379> lpush runoob redis
  (integer) 1
  redis 127.0.0.1:6379> lpush runoob mongodb
  (integer) 2
  redis 127.0.0.1:6379> lpush runoob rabitmq
  (integer) 3
  redis 127.0.0.1:6379> lrange runoob 0 10
  1) "rabitmq"
  2) "mongodb"
  3) "redis"
  redis 127.0.0.1:6379>
  ```

- Set
  
  ```
  redis 127.0.0.1:6379> sadd runoob redis
  (integer) 1
  redis 127.0.0.1:6379> sadd runoob mongodb
  (integer) 1
  redis 127.0.0.1:6379> sadd runoob rabitmq
  (integer) 1
  redis 127.0.0.1:6379> sadd runoob rabitmq
  (integer) 0
  redis 127.0.0.1:6379> smembers runoob
  
  1) "redis"
  2) "rabitmq"
  3) "mongodb"
  ```

- zset(sorted set：有序集合)
  
  - Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。
    
    不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的,但分数(score)却可以重复。
  
  - 命令
    
    ```
    zadd key score member 
    ```
  
  - 实例
    
    ```
    redis 127.0.0.1:6379> zadd runoob 0 redis
    (integer) 1
    redis 127.0.0.1:6379> zadd runoob 0 mongodb
    (integer) 1
    redis 127.0.0.1:6379> zadd runoob 0 rabitmq
    (integer) 1
    redis 127.0.0.1:6379> zadd runoob 0 rabitmq
    (integer) 0
    redis 127.0.0.1:6379> > ZRANGEBYSCORE runoob 0 1000
    1) "mongodb"
    2) "rabitmq"
    3) "redis"
    ```

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
  1.配置文件    /etc/redis/xxxx.conf
  2.日志、数据文件    /data/redis/xxxx
  3.启动脚本    /etc/init.d/redis_xxxx
  ```

- 客户端启动时    redis-cli -p xxxx

```
### Enable remote Redis connection

- Replaced `bind 127.0.0.1` with `bind 0.0.0.0` in the `/etc/redis/redis.conf` file, the line does not have a leading `#` nor space,
- Replaced `protected-mode yes` with `protected-mode no` in this same file,
- Allowed all traffic to port `6379` using `ufw allow 6379` and `ufw allow 6379/tcp`

### secure redis

By default, UFW is not available in CentOS repository. So you will need to install the EPEL repository to your system. You can do this by running the following 

linux command
```

# yum install epel-release -y

```
Once the EPEL repository is installed, you can install UFW by just running the following linux command:
```

# yum install --enablerepo="epel" ufw -y

```
After installing UFW, start UFW service and enable it to start on boot time by running the following 

linux command
```

# ufw enable

```
Next, check the status of UFW with the following linux command. You should see the following output:
```

# ufw status

Status: active 

```
You can also disable UFW firewall by running the following linux command:
```

# ufw disable

```

### 中文乱码

在启动命令中添加 --raw即可