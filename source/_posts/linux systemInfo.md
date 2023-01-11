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
    
    1. top命令，然后输入数字1查看，各项参数如下
       - `us`：用户空间占用 CPU 的百分比。
       - `sy`：内核空间占用 CPU 的百分比。
       - `ni`：调整过优先级的进程占用 CPU 的百分比。
       - `id`：空闲 CPU 的百分比。
       - `wa`：等待 I/O 的 CPU 时间的百分比。
       - `hi`：硬中断（hardware interrupt）占用 CPU 的时间的百分比。
       - `si`：软中断（software interrupt）占用 CPU 的时间的百分比。
       - `st`：虚拟机或者运行在它上面的虚拟 CPU 占用 CPU 的时间的百分比。
    2. 输入mpstat查看
    3. 输入以下命令
    
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

# top

- 进程CPU占用率

  top显示某个进程占用cpu达到100%，则表明该进程正在使用所有可用的 CPU 资源。这通常是因为该进程执行的任务非常耗费 CPU 资源，或者该进程存在某些问题导致 CPU 使用率异常高。

  在 Linux 系统中，每个进程都只能在单个 CPU 核心上运行。但是，系统可以通过调度程序（scheduler）在多个 CPU 核心之间轮换运行进程，从而达到让多个进程同时执行的效果。

## 时区

[CentOS 7 时区设置](https://www.cnblogs.com/zhangeamon/p/5500744.html)
