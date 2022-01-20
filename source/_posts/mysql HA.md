---
title: mysql HA & keepalived
date: 2022-01-19 15:54:47
tags: HA
categories: mysql
---

# mysql数据备份

## 方案二：双主机HA部署

**前提**：准备两个机器master1（172.20.3.113）和master2（172.20.3.114），且分别安装了mysql，其中IP地址根据生产具体ip进行替换

### 一、配置my.cnf信息

- 配置/etc/my.cnf文件（从mysql5.7开始不会自动生成my.cnf文件，所以需要手动创建）my.cnf文件内容大致如下：
  
  ```cnf
  [mysql]
  default-character-set=utf8         #设置mysql客户端默认字符集
  [mysqld]
  port = 3306  #可自行更改端口
  basedir=/usr/local/mysql
  datadir=/usr/local/mysql/data
  max_connections = 500              #最大连接数
  log_bin=mysql-bin
  server_id = 1                            #机器1设置为1，机器2设置为2
  binlog_format=ROW
  auto-increment-increment = 2            #字段变化增量值
  auto-increment-offset = 1               #机器1设置为1，机器2设置为2
  slave-skip-errors = all                 #忽略所有复制产生的错误
  gtid_mode=ON
  enforce-gtid-consistency=ON
  
  character-set-server = utf8
  default-storage-engine = INNODB
  lower_case_table_names = 1
  ```
  
  - [mysql]代表我们使用mysql命令登录mysql数据库时的默认设置 
  
  - [mysqld]代表数据库自身的默认设置
    
    > 注意：机器1和机器2只有server-id不同和auto-increment-offset不同,其他必须相同。
    > 
    > 部分配置项解释如下：
    > 
    > binlog_format= ROW：指定mysql的binlog日志的格式，日志中会记录成每一行数据被修改的形式，然后在 slave 端再对相同的数据进行修改。
    > 
    > auto-increment-increment= 2：表示自增长字段每次递增的量，其默认值是1。它的值应设为整个结构中服务器的总数，本案例用到两台服务器，所以值设为2。
    > 
    > auto-increment-offset= 2：用来设定数据库中自动增长的起点(即初始值)，因为这两能服务器都设定了一次自动增长值2，所以它们的起点必须得不同，这样才能避免两台服务器数据同步时出现主键冲突。
    > 
    > 注：另外还可以在my.cnf配置文件中，添加“binlog_do_db=数据库名”配置项（可以添加多个）来指定要同步的数据库。如果配置了这个配置项，如果没添加在该配置项后面的数据库，则binlog不记录它的事件。

- 切换到datacanvas用户进行mysql启动服务 （建议）
  
  ```
   /usr/local/mysql/support-files/mysql.server start
  ```
  
  或者在已经创建软连接的前提下，切换到root用户，并启动mysql服务
  
  ```
  service mysql restart
  ```

- 客户端登录
  
  ```
  /usr/local/mysql/bin/mysql -uroot -p
  ```
  
    设置可远程登录root用户
  
  ```sql
  GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;
  FLUSH PRIVILEGES;
  ```
  
  > 注意：上面的密码'123456'修改成真实的root密码

#### 开始设置双主备份

- 在master1上操作
  
  ```sql
  先在master2上执行，
  show master status;（获取master_log_file和master_log_pos信息）
  
  在master1上执行
  change master to master_host='172.20.3.114',master_port=3306,master_user='rt',master_password='rt123',master_log_file='mysql-bin.000003',master_log_pos=194;
  
  start slave;
  
  show slave status\G
  ```

- 在master2上操作
  
  ```sql
  先在master1上执行，
  show master status;（获取master_log_file和master_log_pos信息）
  在master2上执行
  change master to master_host='172.20.3.113',master_port=3306,master_user='rt',master_password='rt123',master_log_file='mysql-bin.000004',master_log_pos=194;
  
  start slave;
  
  show slave status\G
  ```

---

### 二、keepalived安装配置

需要在master1和master2的机器上安装keepalived服务，安装过程大致如下：

- 通过地址https://pkgs.org/download/keepalived下载相应的安装版本，然后解压的相关目录。

- 源码的安装一般由3个步骤组成：配置（configure）、编译（make）、安装( make install）
  
  ```
  ./configure --prefix=/usr/local/keepalived
  ```
  
   如果提示错误信息
  
  ```
  configure: error: 
    !!! OpenSSL is not properly installed on your system. !!!
    !!! Can not include OpenSSL headers files.            !!!
  ```
  
  需要安装yum install openssl openssl-devel（RedHat系统），
  再次执行./configure --prefix=/usr/local/keepalived

- 在安装目录执行`make && make install`进行编译安装

- keepalived配置文件，默认情况下keepalived启动时会去/etc/keepalived目录下加载配置文件keepalived.conf

```conf
! Configuration File forkeepalived
global_defs {
notification_email {
[email protected]
 }
notification_email_from  [email protected]
smtp_server 127.0.0.1
smtp_connect_timeout 30
router_id MYSQL_HA      #标识，双主相同
 }
vrrp_instance VI_1 {
 state BACKUP           #两台都设置BACKUP
 interface eth0         #网卡名称
 virtual_router_id 51       #主备相同
 priority 100   #优先级，另一台改为90    
 advert_int 1    
 nopreempt  #不抢占，只在优先级高的机器上设置即可，优先级低的机器不设置    
 authentication {
 auth_type PASS    #鉴权，默认通过
 auth_pass 1111    # 鉴权访问密码
 }
 virtual_ipaddress {
  172.20.3.200    #虚拟ip
 }
}

virtual_server 172.20.3.200 3306 {    
     delay_loop 2   #每个2秒检查一次real_server状态    
     lb_algo wrr   #LVS算法    
     lb_kind DR    #LVS模式    
     persistence_timeout 60   #会话保持时间    
     protocol TCP    
     real_server 172.20.3.113 3306 {    
         weight 1    #指定了当前主机的权重    
         notify_down /usr/local/keepalived/kill_keepalived.sh  #检测到服务down后执行的脚本    
         TCP_CHECK {    
             connect_timeout 10    #连接超时时间
             delay_before_retry 3   #重连间隔时间    
             connect_port 3306   #健康检查端口  
         }  
     }
     real_server 172.20.3.114 3306 {
        weight 2
        notify_down /usr/local/keepalived/kill_keepalived.sh  #检测到服务down后执行的脚本
        TCP_CHECK {
            connect_timeout 10
            delay_before_retry 3
            connect_port 3306
        }
    }  
}
```

注意：参数priority两个服务器配置不同，其中virtual_ipaddress是虚拟ip，之后项目可通过访问 172.20.3.200:3306进行访问双主mysql机群。

上述配置中会涉及/usr/local/keepalived/kill_keepalived.sh，分别在两台服务器上编写kill_keepalived.sh脚本内容：

```
#!/bin/bash
pkill keepalived
```

   然后给脚本加权限

```chmod
chmod +x /usr/local/keepalived/kill_keepalived.sh
```

- 启动keepalived服务
  
  ```
  service keepalived start
  ```
  
  如果启动失败，尝试输入`pkill -9 keepalived`，然后再尝试重启

---

### 三、访问双主mysql集群

两台机器的mysql和keepalived配置完成之后，即可在项目中，通过访问虚拟ip地址（172.20.3.200:3306）进行mysql集群的访问。
