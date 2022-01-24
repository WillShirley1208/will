---
title: linux snippet
date: 2017-05-9 10:01:30
tags: snippet
categories: linux
---

- /bin/    用以存储二进制可执行命令文件，/usr/bin/也存储了一些基于用户的命令文件。
- /sbin/    许多系统命令的存储位置，/usr/sbin/中也包括了许多命令。
- /root/    超级用户，即根用户的主目录。
- /home/    普通用户的默认目录，在该目录下，每个用户拥有一个以用户名命名的文件夹。
- /boot/    存放Ubuntu内核和系统启动文件。
- /mnt/     通常包括系统引导后被挂载的文件系统的挂载点。
- /dev/    存储设备文件，包括计算机的所有外部设备，如硬盘、是、键盘、鼠标等。
- /etc/    存放文件管理配置文件和目录。
- /lib/    存储各种程序所需要的共享库文件。
- /lost+found/    一般为空，当非法关机时，会存放一些零散的文件。
- /var/    用于存放很多不断变化的文件，例如日志文件等。
- /usr/    包括与系统用户直接有关的文件和目录
- /media/    存放Ubuntu系统自动挂载的设备文件。
- /proc/    这是一个虚拟目录，它是内存的映射，包括系统信息和进程信息。
- /tmp/    存储系统和用户的临时信息。
- /initrd/    用来加载启动时临时挂载的initrd.img映像文件，以及载入所要的设备模块目录。
- /opt/    作为可选文件和程序的存放目录，否则将无法引导计算机进入操作系统。
- /srv/    存储系统提供的服务数据。
- /sys/    系统设备和文件层次结构，并向用户程序提供详细的内核数据信息。

## 装机问题

### Wifi Not Enabled Acer Aspire 4750

```shell
~$ rfkill list all
0: phy0: Wireless LAN
Soft blocked: no
Hard blocked: yes

~$ sudo rmmod acer-wmi
​```

add below line at the end of the file : /etc/modprobe.d/blacklist.conf
blacklist acer-wmi

Then restart the system.
~$ rfkill list all
0: phy0: Wireless LAN
Soft blocked: no
Hard blocked: no
```

## 服务

- 防火墙 firewalld.service

## shell

**shell与export命令**

用户登录到Linux系统后，系统将启动一个用户shell。在这个shell中，可以使用shell命令或声明变量，也可以创建并运行 shell脚本程序。运行shell脚本程序时，系统将创建一个子shell。此时，系统中将有两个shell，一个是登录时系统启动的shell，另一 个是系统为运行脚本程序创建的shell。当一个脚本程序运行完毕，它的脚本shell将终止，可以返回到执行该脚本之前的shell。从这种意义上来 说，用户可以有许多 shell，每个shell都是由某个shell（称为父shell）派生的。

在子 shell中定义的变量只在该子shell内有效。如果在一个shell脚本程序中定义了一个变量，当该脚本程序运行时，这个定义的变量只是该脚本程序内 的一个局部变量，其他的shell不能引用它，要使某个变量的值可以在其他shell中被改变，可以使用export命令对已定义的变量进行输出。 export命令将使系统在创建每一个新的shell时定义这个变量的一个拷贝。这个过程称之为变量输出。

### 操作文件名中含有“-”的文件

- 使用转移字符 “--”， 比如查看文件“-abc.txt“，命令为 `vim -- -abc.txt`

### KVM

KVM 的全称是：Kernel-based Virtual Machine，简单一句话概括，就是一个基于 Linux 内核的虚拟化管理系统。