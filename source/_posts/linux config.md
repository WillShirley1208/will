---
title: linux config
date: 2015-01-16 19:01:30
tags: config
categories: linux
---

### java config

- use `java -XshowSettings:properties` to show the java.library.path (and others) value.

### native lib config

- `/lib` contain "essential" libraries that may be needed even in single-user (admin-only) mode and without /usr mounted
- `/usr/lib` are the libraries for the normal user-programs, that mostly can be found under /usr.
- `/usr/local/lib` are the libraries for locally installed programs and packages ie. things you've compiled and installed from source-packages yourself.

### other

- `/etc/environment`是设置整个系统的环境，而`/etc/profile`是设置所有用户的环境 

### vmware centos ip config

1. ` vi /etc/sysconfig/network-scripts/ifcfg-ens33`,设置`ONBOOT=yes`
2. `service network restart `
3. `ip a`查看ip

### ssh

1. 进入目录` /root/.ssh`

2. 执行命令：`ssh-keygen -t rsa`，会生成文件id_rsa和id_rsa.pub

3. 将公钥写入文件 authorized_keys，`cat id_rsa.pub >> authorized_keys`

4. 在其它机器重复上述操作

5. 将其它机器的公钥拷贝到第一台机器的authorized_keys文件中
   
   `ssh-copy-id -i id_rsa.pub 用户@ip`
   
   > 注意，一定不要使用复制拷贝的方式把公钥复制到authorized_keys文件中，因为复制的时候会有特殊字符的加入，可以在vim中使用`:set list`进行验证

6. 将第一台机器的authorized_keys复制到其它机器上
   
   `scp authorized_keys 用户@ip:/路径/.ssh/`
   
   注意点
- authorized_keys 的权限是600，即读和写（如果默认不是的话，需要修改）

### 界面卡死

1. 使用 Ctrl+Alt+F1或F2或到F6，进入tty终端（输入用户名和密码）

2. 执行命令注销桌面重新登录
   
   ```shell
   sudo pkill Xorg
   或
   sudo restart lightdm
   ```