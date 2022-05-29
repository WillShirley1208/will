---
title: windows snippet
date: 2018/5/22 16:44
tags: snippet
categories: windows
---

## 进程操作

- netstat -aon|findstr "8080" 

TCP     127.0.0.1:80       0.0.0.0:0             LISTENING    2448
端口被进程号为2448的进程占用，继续执行下面命令：

- tasklist|findstr "2448" 

thread.exe                   2016 Console                 0     16,064 K

很清楚，thread占用了你的端口,Kill it

- taskkill -F -PID 2448

如果第二步查不到，那就开任务管理器，进程---查看---选择列---pid（进程位标识符）打个勾就可以了



## 查看文件md5值

```shell
certutil -hashfile filename MD5
certutil -hashfile filename SHA1
certutil -hashfile filename SHA256
```