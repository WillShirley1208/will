### beeline

1）metadata ：hive元数据，即hive定义的表名，字段名，类型，分区，用户这些数据。一般存储关系型书库mysql中，在测试阶段也可以用hive内置Derby数据库。

（2）metastore ：hivestore服务端。主要提供将DDL，DML等语句转换为MapReduce，提交到hdfs中。

（3）hiveserver2：hive服务端。提供hive服务。客户端可以通过beeline，jdbc（即用java代码链接）等多种方式链接到hive。

（4）beeline：hive客户端链接到hive的一个工具。可以理解成mysql的客户端。如：navite cat 等。

![](https://img-blog.csdnimg.cn/20191122115956341.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NDQwMDQw,size_16,color_FFFFFF,t_70)

2 连接hive：
（1）./bin/hive        
通过 ./bin/hive 启动的hive服务，第一步会先启动metastore服务，然后在启动一个客户端连接到metastore。此时metastore服务端和客户端都在一台机器上，别的机器无法连接到metastore，所以也无法连接到hive。这种方式不常用，一直只用于调试环节。

（2） ./bin/hive  --service metastore
**通过hive --service metastore 会启动一个 hive metastore服务默认的端口号为：9083。metastore服务里面配置metadata相关的配置。此时可以有多个hive客户端在hive-site.xml配置hive.metastore.uris=thrift://ipxxx:9083  的方式链接到hive。motestore 虽然能使hive服务端和客户端分别部署到不同的节点，客户端不需要关注metadata的相关配置。但是metastore只能通过只能通过配置hive.metastore.uris的方式连接，无法通过jdbc的方式访问。**

（3）./bin/hiveserver2
hiveserver2 会启动一个hive服务端默认端口为：10000，可以通过beeline，jdbc，odbc的方式链接到hive。**hiveserver2启动的时候会先检查有没有配置hive.metastore.uris，如果没有会先启动一个metastore服务，然后在启动hiveserver2。如果有配置hive.metastore.uris。会连接到远程的metastore服务。这种方式是最常用的。**部署在图如下：

- 登录bin/beeline，可以启动客户端链接到hiveserver2。执行beeline后在控制输入 !connect jdbc:hive2://localhost:10000/default root 123 就可以链接到 hiveserver2了；default表示链接到default database， root 和123 分别为密码。注意这里的密码不是mysql的密码，是hive中的用户

```
连接库
!connect jdbc:hive2://localhost:10000/default root 123
```





# hive中几种分割符

**分隔符**

\n    每行一条记录
^A    分隔列（八进制 \001）
^B    分隔ARRAY或者STRUCT中的元素，或者MAP中多个键值对之间分隔（八进制 \002）
^C    分隔MAP中键值对的“键”和“值”（八进制 \003）

**用到了系统默认分隔符。通常下面2中情况我们需要需要用到分隔符**

1，制作table的输入文件，有时候我们需要输入一些特殊的分隔符

2，把hive表格导出到本地时，系统默认的分隔符是^A，这个是特殊字符，直接cat或者vim是看不到的

**分隔符在HIVE中的用途**

| 分隔符     | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| \n         | 对于文本文件来说，每行都是一条记录，因此换行符可以分隔记录   |
| ^A(Ctrl+A) | 用于分隔字段(列)。在CREATE TABLE语句中可以使用八进制编码\001表示 |
| ^B(Ctrl+B) | 用于分隔ARRAY或者STRUCT中的元素，或用于MAP中键-值对之间的分隔。在CREATE TABLE语句中可以使用八进制编码\002表示 |
| ^C(Ctrl+C) | 用于MAP中键和值之间的分隔。在CREATE TABLE语句中可以使用八进制编码\003表示 |

>  
>
> Hive 中没有定义专门的数据格式，数据格式可以由用户指定，用户定义数据格式需要指定三个属性：列分隔符（通常为空格、”\t”、”\x001″）、行分隔符（”\n”）以及读取文件数据的方法。由于在加载数据的过程中，不需要从用户数据格式到 Hive 定义的数据格式的转换，因此，Hive 在加载的过程中不会对数据本身进行任何修改，而只是将数据内容复制或者移动到相应的 HDFS 目录中。

我们可以在create表格的时候，选择如下，表格加载input的文件的时候就会按照下面格式匹配

```
row format delimited 
fields terminated by '\001' 
collection items terminated by '\002' 
map keys terminated by '\003'
lines terminated by '\n' 
stored as textfile;
```

 ### 如何查看和修改分割符，特殊符号

1. 查看隐藏字符的方法

1.1，cat -A filename

![img](http://image.okcode.net/26FFE1BCC5620E19E94B26122C71BA2E.png)

1.2，vim filename后 命令模式下输入

```
set list显示特殊符号
set nolist 取消显示特殊符号
```

2. 修改隐藏字符的方法

首先按照1.2打开显示特殊符号。进入INSERT模式

```
ctrl + V 可以输入 ^符号
ctrl + a 可以输入A---'\001'
ctrl + b 可以输入A---'\002'
ctrl + c 可以输入A---'\003'
```

 注意：虽然键盘上你能找到^和A但直接输入时不行的，必须按照上面的方法输入。

第一行是特殊符号颜色蓝色，第二行直接输入不是特殊符号。

![img](http://image.okcode.net/DD9ED976ABB6F4313B8F0F7C2DD5C33E.png)

特殊号直接cat是不可以看见的，但是第二行是可见的，所以不是特殊符号。

![img](http://image.okcode.net/75D96F800A1815F7A84A8CF543BD7063.png)

```
ROW FORMAT DELIMITED 
FIELDS TERMINATED BY '\u0001' 
COLLECTION ITEMS TERMINATED BY '\u0002' 
MAP KEYS TERMINATED BY '\u0003'
\u0001是ASCII编码值，对应java代码中的"\001"
```

意义如下：

（1）FIELDS，字段之间的分隔符是'\u0001'

（2）COLLECTION ITEMS，多个集合之间的分隔符是'\u0002'，例如（kv1，kv2，kv3）这种多个键值对之间的分隔符就是'\u0002'

（3）MAP KEYS，单个map的k和v之间的分隔符是\\u0003\，例如kv1里，k \u0003 v





### 查看orc文件

```shell
hive --orcfiledump <hdfs-location-of-orc-file>
```



### 修改字段类型

```sql
ALTER TABLE <table-name> CHANGE <old-col-name> <new-col-name> <data-type>;
ALTER TABLE employee CHANGE e_id e_id INT;
```



### 建表

- Create ORC table

- Login to the web console

- Launch Hive by typing `hive` in the web console. Run the below commands in Hive.

- Use your database by using the below command. `${env:USER}` gets replaced by your username automatically:

  ```
  use ${env:USER};
  ```

- To create an ORC file format:

  ```
  CREATE TABLE orc_table (
      first_name STRING, 
      last_name STRING
   ) 
   STORED AS ORC;
  ```

- To insert values in the table:

  ```
  INSERT INTO orc_table VALUES ('John','Gill');
  ```

- To retrieve all the values in the table:

  ```
  SELECT * FROM orc_table;
  ```