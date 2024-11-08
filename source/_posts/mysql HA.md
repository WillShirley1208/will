---
title: mysql HA & keepalived
date: 2022-01-19 15:54:47
tags: HA
categories: mysql
---

# 知识点

- the size of blob column

  ```
    A BLOB can be 65535 bytes (64 KB) maximum.
  If you need more consider using:
  a MEDIUMBLOB for 16777215 bytes (16 MB)
  a LONGBLOB for 4294967295 bytes (4 GB).
  ```

- join sql

- string convert to timstamp

  `SELECT STR_TO_DATE('2014-05-28 11:30:10','%Y-%m-%d %H:%i:%s');`

### sql语句

- IN

确定给定的值是否与子查询或列表中的值相匹配。in在查询的时候，首先查询子查询的表，然后将内表和外表做一个笛卡尔积，然后按照条件进行筛选。所以相对内表比较小的时候，in的速度较快。 

```sql
SELECT
    *
FROM
    `user`
WHERE
    `user`.id IN (
        SELECT
            `order`.user_id
        FROM
            `order`
    )
```

以上查询使用了in语句,in()只执行一次,它查出B表中的所有id字段并缓存起来.之后,检查A表的id是否与B表中的id相等,如果相等则将A表的记录加入结果集中,直到遍历完A表的所有记录。

可以看出,当B表数据较大时不适合使用in(),因为它会B表数据全部遍历一次. 如:A表有10000条记录,B表有1000000条记录,那么最多有可能遍历10000`*`1000000次,效率很差. 再如:A表有10000条记录,B表有100条记录,那么最多有可能遍历10000*100次,遍历次数大大减少,效率大大提升。

- exists

指定一个子查询，检测行的存在。遍历循环外表，然后看外表中的记录有没有和内表的数据一样的。匹配上就将结果放入结果集中。 

```sql
select a.* from A a where exists(select 1 from B b where a.id=b.id)
```

以上查询使用了exists语句,exists()会执行A.length次,它并不缓存exists()结果集,因为exists()结果集的内容并不重要,重要的是结果集中是否有记录,如果有则返回true,没有则返回false。

当B表比A表数据大时适合使用exists(),因为它没有那么遍历操作,只需要再执行一次查询就行. 如:A表有10000条记录,B表有1000000条记录,那么exists()会执行10000次去判断A表中的id是否与B表中的id相等. 如:A表有10000条记录,B表有100000000条记录,那么exists()还是执行10000次,因为它只执行A.length次,可见B表数据越多,越适合exists()发挥效果. 再如:A表有10000条记录,B表有100条记录,那么exists()还是执行10000次,还不如使用in()遍历10000*100次,因为in()是在内存里遍历比较,而exists()需要查询数据库,我们都知道查询数据库所消耗的性能更高,而内存比较很快. 

- where

sql查询条件中`where 1=1,1=2和1=0`，这种写法，主要是为了拼凑动态的sql语句，如果使用不好会起到副作用的，是根据个人的一些习惯，是为了避免where 关键字后面的第一个词直接就是 “and”而导致语法错误，是为了后面附加and ...方便程序逻辑处理用的。 

- select count(*)和select count(1)的区别

  ```
  一般情况下，Select Count(*)和Select Count(1)两着返回结果是一样的，假如表没有主键(Primary key), 那么count(1)比count(*)快，如果有主键的话，那主键作为count的条件时候count(主键)最快，如果你的表只有一个字段的话那count(*)就是最快的。
  ```

### 事物隔离级别

- read uncommitted（读取未提交数据）

  > 我们将事务隔离级别设置为read uncommitted，即便是事务没有commit，但是我们仍然能读到未提交的数据，这是所有隔离级别中最低的一种。
  >
  > 脏读

- read committed（可以读取其他事务提交的数据）

  > 大多数数据库默认的隔离级别;
  >
  > 当我们将当前会话的隔离级别设置为read committed的时候，当前会话只能读取到其他事务提交的数据，未提交的数据读不到。

- repeatable read（可重读）

  > MySQL默认的隔离级别
  >
  > 当我们将当前会话的隔离级别设置为repeatable read的时候，当前会话可以重复读，就是每次读取的结果集都相同，而不管其他事务有没有提交。
  >
  > 幻读

- serializable（串行化）

  > 当我们将当前会话的隔离级别设置为serializable的时候，其他会话对该表的写操作将被挂起。可以看到，这是隔离级别中最严格的，但是这样做势必对性能造成影响。所以在实际的选用上，我们要根据当前具体的情况选用合适的。

### DDL VS DML

DML statements are SQL statements that manipulate data. DML stands for Data Manipulation Language. The SQL statements that are in the DML class are INSERT, UPDATE and DELETE. Some people also lump the SELECT statement in the DML classification.

Data Definition Languages (DDL) are used to define the database structure. Any CREATE, DROP and ALTER commands are examples of DDL SQL statements.

### 设置远程登录

- mysql 8版本设置可用root远程访问服务

  ```mysql
  CREATE USER 'root'@'%' IDENTIFIED BY 'password';
  
  GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
  
  FLUSH PRIVILEGES;
  ```

  

### mysqldump

- 导出数据库

  ```shell
  mysqldump -h127.0.0.1 -P3306 -uroot -p database > file.sql
  其中 -h和-P可以省略（不过mysql在docker中不可以省略）,database是数据库名称
  ```

- 导出数据表数据（根据sql的where条件）

  ```shell
  mysqldump -uroot -p database --tables tablename --where="id>8" > result.sql 
  ```

- 执行sql文件

  ```shell
  mysql> source /path/to/files/file.sql
  ```

- 导出所有数据库

  ```shell
  mysqldump -u root -p --all-databases > backup_filename.sql
  ```

- 只导出建表语句

  ```sql
  mysqldump -u your_username -p --no-data your_database > backup.sql
  ```

### 新增主键

suppose you don't have column for auto increment like id, no, then you can add using following query:

```sql
ALTER TABLE table_name ADD id int NOT NULL AUTO_INCREMENT primary key FIRST
```

If you've column, then alter to auto increment using following query:

```sql
 ALTER TABLE table_name MODIFY column_name datatype(length) AUTO_INCREMENT PRIMARY
```



# mysql数据备份

## 方案一：定期备份数据库数据文件

### 一、编写shell脚本

脚本文件**backup_mysql.sh**信息如下：

```shell
#用户名
username=root
#密码
password=填写密码
#将要备份的数据库
database_name=填写需要备份的数据库

#保存备份文件最多个数
count=30
#备份保存路径
backup_path=/data/mysql_backup
#日期
date_time=`date +%Y-%m-%d-%H-%M`

#如果文件夹不存在则创建
if [ ! -d $backup_path ]; 
then     
    mkdir -p $backup_path; 
fi
#开始备份
mysqldump -u $username -p$password $database_name > $backup_path/$database_name-$date_time.sql
#开始压缩
cd $backup_path
tar -zcvf $database_name-$date_time.tar.gz $database_name-$date_time.sql
#删除源文件
rm -rf $backup_path/$database_name-$date_time.sql
#更新备份日志
echo "create $backup_path/$database_name-$date_time.tar.gz" >> $backup_path/dump.log

#找出需要删除的备份
delfile=`ls -l -crt $backup_path/*.tar.gz | awk '{print $9 }' | head -1`

#判断现在的备份数量是否大于阈值
number=`ls -l -crt  $backup_path/*.tar.gz | awk '{print $9 }' | wc -l`

if [ $number -gt $count ]
then
  #删除最早生成的备份，只保留count数量的备份
  rm $delfile
  #更新删除文件日志
  echo "delete $delfile" >> $backup_path/dump.log
fi
```

该脚本实现的功能：备份指定数据库的数据信息到指定目录，并只保存指定数量的最新文件。

注意：脚本中需要补全脚本中的**password**和**database_name**信息，可修改备份保存路径**backup_path**，以及最多保存的备份文件数量**count**。

编写完脚本信息之后，需要给脚本赋予可执行权限 `chmod +x backup_mysql.sh`

### 二、设定定时任务crontab

运行crontab -e命令，打开一个可编辑的文本，输入`0 1 * * * /path/to/backup_mysql.sh`  保本并退出即添加完成。

注意：其中`0 1 * * *`，表示每天凌晨1点进行备份操作，可自行修改1的值（范围0～23）

其中路径信息`/path/to/backup_mysql.sh`需要修改为实际的脚本路径。

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
