---
title: vmware snippet
date: 2019-04-20 16:15:30
tags: snippet
categories: vmware
---



### 用户

- 新增用户

  `adduser username`

  `passwd username`

  `usermod -aG wheel username`

  `su - username`

### IP

- 安装完毕之后，需要设置IP分配

  1. `cd /etc/sysconfig/network-scripts/`

  2. 再进入编辑`ifcfg-e**` 文件 执行命令 
     `vi ifcfg-e**`
  3. 修改ONBOOT项

  ```
  其中部分内容如下： 
  DEVICE=eth0 #设备名称，可根据ifcofnig命令查看到。 
  BOOTPROTO=dhcp #连接方式，dhcp会自动分配地址，此时不需要在下面设置ip和网关 
  ONBOOT=yes #yes表示启动就执行该配置，需要改为yes
  ```

  4. 修改完后需要重启网络设置，执行

     `service network restart`

- 设置静态IP

  - [虚拟机设置](https://blog.csdn.net/clevercode/article/details/50574695)

  - 宿主机器设置

    需要设置vmnet8的ip为同一网段的配置

### jdk

- 安装jdk

  `sudo yum install java-1.8.0-openjdk-devel`

  - find path

  `update-alternatives --config java`

  - config .bashrc

  `export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.191.b12-1.el7_6.x86_64/jre`

### mysql

- 授权所有机器可以从外界访问mysql

  `GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'will';`

  `FLUSH PRIVILEGES;`