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

