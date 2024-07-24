---
title: casdoor point
date: 2024-06-05 17:42:30
tags: point
categories: casdoor
---



## deploy

- docker

  ```shell
  sudo docker run -d -p 18000:8000 \
    -v /usr/share/zoneinfo/Asia/Shanghai:/etc/localtime:ro \
    -v /home/dingodb/dongwei/docker/timezone:/etc/timezone:ro \
    casbin/casdoor-all-in-one
  ```

  > 容器内部服务端口8000，映射成对外访问接端口 18000

