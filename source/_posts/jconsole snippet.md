---
title: jconsole snippet
date: 2021-12-11 21:05:29
tags: snippet
categories: java
---

### jconsole配置远程监控

- 远程jvm进程需配置
  
  ```shell
  env.java.opts: 
  -Dcom.sun.management.jmxremote 
  -Dcom.sun.management.jmxremote.port=9999
  -Dcom.sun.management.jmxremote.authenticate=false 
  -Dcom.sun.management.jmxremote.ssl=false
  ```
  
  > 其中9999为指定监控端口
