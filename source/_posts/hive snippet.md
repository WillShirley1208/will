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