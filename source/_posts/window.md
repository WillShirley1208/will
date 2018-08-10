---
title: window进程操作
date: 2018/5/22 16:44
tags: 知识点
categories: 归纳总结
top_img: http://p9kvv0fqj.bkt.clouddn.com/windows.jpg
---

- netstat -aon|findstr "8080" 

TCP     127.0.0.1:80       0.0.0.0:0             LISTENING    2448
端口被进程号为2448的进程占用，继续执行下面命令：

- tasklist|findstr "2448" 

thread.exe                   2016 Console                 0     16,064 K

很清楚，thread占用了你的端口,Kill it

- taskkill -F -PID 2448

如果第二步查不到，那就开任务管理器，进程---查看---选择列---pid（进程位标识符）打个勾就可以了