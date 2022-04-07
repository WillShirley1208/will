---
title: linux systemInfo command
date: 2015-10-16 12:01:30
tags: command
categories: linux
---

## 查看系统配置

- 查看系统
  
  - `cat /etc/os-release`

- 查看内核
  
  - `cat /proc/version`
  - `uname -a`

- 查看linux版本
  
  - `lsb_release -a`
  - `cat /etc/issue`

- > 总核数 = 物理CPU个数 X 每颗物理CPU的核数 
  > 
  > 总逻辑CPU数 = 物理CPU个数 X 每颗物理CPU的核数 X 超线程数
  
  - 查看物理CPU个数
    
    ```shell
    cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l
    ```
  
  - 查看每个物理CPU中core的个数(即核数)
    
    ```shell
    cat /proc/cpuinfo| grep "cpu cores"| uniq
    ```
  
  - 查看逻辑CPU的总数
    
    ```shell
    cat /proc/cpuinfo| grep "processor"| wc -l
    ```

## 时区

[CentOS 7 时区设置](https://www.cnblogs.com/zhangeamon/p/5500744.html)
