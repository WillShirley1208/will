---
title: flink sql
date: 2022-01-04 17:55:24
tags: point
categories: flink
---

# table vs view

> Temporary tables are always stored in memory and only exist for the duration of the Flink session they are created within. These tables are not visible to other sessions. They are not bound to any catalog or database but can be created in the namespace of one. Temporary tables are not dropped if their corresponding database is removed.

> Tables can be either virtual (VIEWS) or regular (TABLES). VIEWS can be created from an existing Table object, usually the result of a Table API or SQL query. TABLES describe external data, such as a file, database table, or message queue.

# grammer

- NOT ENFORCED

> If you know that the data conforms to these constraints, you can use the NOT ENFORCED capability to help achieve two goals:
> 
> - Improve performance, primarily in insert, update, and delete operations on the table
> - Reduce space requirements that are associated with enforcing a primary key or unique constraint

# data type

### 字符串类型：

- ⭐ CHAR、CHAR(n)：定长字符串，就和 Java 中的 Char 一样，n 代表字符的定长，取值范围 [1, 2,147,483,647]。如果不指定 n，则默认为 1。
- ⭐ VARCHAR、VARCHAR(n)、STRING：可变长字符串，就和 Java 中的 String 一样，n 代表字符的最大长度，取值范围 [1, 2,147,483,647]。如果不指定 n，则默认为 1。STRING 等同于 VARCHAR(2147483647)。

### 二进制字符串类型：

- ⭐ BINARY、BINARY(n)：定长二进制字符串，n 代表定长，取值范围 [1, 2,147,483,647]。如果不指定 n，则默认为 1。
- ⭐ VARBINARY、VARBINARY(n)、BYTES：可变长二进制字符串，n 代表字符的最大长度，取值范围 [1, 2,147,483,647]。如果不指定 n，则默认为 1。BYTES 等同于 VARBINARY(2147483647)。

### 精确数值类型：

- ⭐ DECIMAL、DECIMAL(p)、DECIMAL(p, s)、DEC、DEC(p)、DEC(p, s)、NUMERIC、NUMERIC(p)、NUMERIC(p, s)：固定长度和精度的数值类型，就和 Java 中的 BigDecimal 一样，p 代表数值位数（长度），取值范围 [1, 38]；s 代表小数点后的位数（精度），取值范围 [0, p]。如果不指定，p 默认为 10，s 默认为 0。
- ⭐ TINYINT：-128 到 127 的 1 字节大小的有符号整数，就和 Java 中的 byte 一样。
- ⭐ SMALLINT：-32,768 to 32,767 的 2 字节大小的有符号整数，就和 Java 中的 short 一样。
- ⭐ INT、INTEGER：-2,147,483,648 to 2,147,483,647 的 4 字节大小的有符号整数，就和 Java 中的 int 一样。
- ⭐ BIGINT：-9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 的 8 字节大小的有符号整数，就和 Java 中的 long 一样。

### 有损精度数值类型：

- ⭐ FLOAT：4 字节大小的单精度浮点数值，就和 Java 中的 float 一样。
- ⭐ DOUBLE、DOUBLE PRECISION：8 字节大小的双精度浮点数值，就和 Java 中的 double 一样。
- ⭐ 关于 FLOAT 和 DOUBLE 的区别可见 https://www.runoob.com/w3cnote/float-and-double-different.html

### 日期、时间类型：

- ⭐ DATE：由 `年-月-日` 组成的 `不带时区含义` 的日期类型，取值范围 [0000-01-01, 9999-12-31]

- ⭐ TIME、TIME(p)：由 `小时：分钟：秒[.小数秒]` 组成的 `不带时区含义` 的的时间的数据类型，精度高达纳秒，取值范围 [00:00:00.000000000到23:59:59.9999999]。其中 p 代表小数秒的位数，取值范围 [0, 9]，如果不指定 p，默认为 0。

- ⭐ TIMESTAMP、TIMESTAMP(p)、TIMESTAMP WITHOUT TIME ZONE、TIMESTAMP(p) WITHOUT TIME ZONE：由 `年-月-日 小时：分钟：秒[.小数秒]` 组成的 `不带时区含义` 的时间类型，取值范围 [0000-01-01 00:00:00.000000000, 9999-12-31 23:59:59.999999999]。其中 p 代表小数秒的位数，取值范围 [0, 9]，如果不指定 p，默认为 6。

  Timestamp precision can range from 0 (seconds) to 9 (nanoseconds). The default precision is 6.

- ⭐ TIMESTAMP WITH TIME ZONE、TIMESTAMP(p) WITH TIME ZONE：由 `年-月-日 小时：分钟：秒[.小数秒] 时区` 组成的 `带时区含义` 的时间类型，取值范围 [0000-01-01 00:00:00.000000000 +14:59, 9999-12-31 23:59:59.999999999 -14:59]。其中 p 代表小数秒的位数，取值范围 [0, 9]，如果不指定 p，默认为 6。

- ⭐ TIMESTAMP_LTZ、TIMESTAMP_LTZ(p)：由 `年-月-日 小时：分钟：秒[.小数秒] 时区` 组成的 `带时区含义` 的时间类型，取值范围 [0000-01-01 00:00:00.000000000 +14:59, 9999-12-31 23:59:59.999999999 -14:59]。其中 p 代表小数秒的位数，取值范围 [0, 9]，如果不指定 p，默认为 6。

- ⭐ TIMESTAMP_LTZ 与 TIMESTAMP WITH TIME ZONE 的区别在于：TIMESTAMP WITH TIME ZONE 的时区信息是携带在数据中的，举例：其输入数据应该是 2022-01-01 00:00:00.000000000 +08:00；TIMESTAMP_LTZ 的时区信息不是携带在数据中的，而是由 Flink SQL 任务的全局配置决定的，我们可以由 `table.local-time-zone` 参数来设置时区。

- ⭐ INTERVAL YEAR TO MONTH、 INTERVAL DAY TO SECOND：interval 的涉及到的种类比较多。INTERVAL 主要是用于给 TIMESTAMP、TIMESTAMP_LTZ 添加偏移量的。举例，比如给 TIMESTAMP 加、减几天、几个月、几年。INTERVAL 子句总共涉及到的语法种类如下 Flink SQL 案例所示。

​	**注意：但是在 Flink SQL 中，无法直接去掉时间字段的小数秒部分而保留时间类型。**

### 其它类型

1. 布尔类型：BOOLEAN
2. ⭐ NULL 类型：NULL
3. ⭐ Raw 类型：RAW('class', 'snapshot') 。只会在数据发生网络传输时进行序列化，反序列化操作，可以保留其原始数据。以 Java 举例，`class` 参数代表具体对应的 Java 类型，`snapshot` 代表类型在发生网络传输时的序列化器

# 时间属性

>  像窗口（在 [Table API](https://nightlies.apache.org/flink/flink-docs-master/zh/docs/dev/table/tableapi/#group-windows) 和 [SQL](https://nightlies.apache.org/flink/flink-docs-master/zh/docs/dev/table/sql/queries/window-agg/) ）这种基于时间的操作，需要有时间信息。因此，Table API 中的表就需要提供*逻辑时间属性*来表示时间，以及支持时间相关的操作。

每种类型的表都可以有时间属性，时间属性可以通过

1. 用CREATE TABLE DDL创建表的时候指定
2. 可以在 `DataStream` 中指定
3. 可以在定义 `TableSource` 时指定。

一旦时间属性定义好，它就可以像普通列一样使用，也可以在时间相关的操作中使用，只要时间属性没有被修改，而是简单地从一个表传递到另一个表，它就仍然是一个有效的时间属性。

时间属性可以像普通的时间戳的列一样被使用和计算。一旦时间属性被用在了计算中，它就会被物化，进而变成一个普通的时间戳。普通的时间戳是无法跟 Flink 的时间以及watermark等一起使用的，所以普通的时间戳就无法用在时间相关的操作中（这句话是只限于被修改的普通时间戳，还是包含未被修改的时间戳）。

### 处理时间

> 处理时间是基于机器的本地时间来处理数据，它是最简单的一种时间概念，但是它不能提供确定性。它既不需要从数据里获取时间，也不需要生成 watermark。

定义处理时间的三种方法:

1. 在创建表的 DDL 中定义

   > 用 `PROCTIME()` 就可以定义处理时间，函数 `PROCTIME()` 的返回类型是 TIMESTAMP_LTZ 

   ```java
   CREATE TABLE user_actions (
     user_name STRING,
     data STRING,
     user_action_time AS PROCTIME() -- 声明一个额外的列作为处理时间属性
   ) WITH (
     ...
   );
   ```

2. 在 DataStream 到 Table 转换时定义

   > 处理时间属性可以在 schema 定义的时候用 `.proctime` 后缀来定义。时间属性一定不能定义在一个已有字段上，所以它只能定义在 schema 定义的最后。

   ```java
   DataStream<Tuple2<String, String>> stream = ...;
   
   // 声明一个额外的字段作为时间属性字段
   Table table = tEnv.fromDataStream(stream, $("user_name"), $("data"), $("user_action_time").proctime());
   
   WindowedTable windowedTable = table.window(
           Tumble.over(lit(10).minutes())
               .on($("user_action_time"))
               .as("userActionWindow"));
   ```

3. 使用 TableSource 定义

   ```java
   // 定义一个由处理时间属性的 table source
   public class UserActionSource implements StreamTableSource<Row>, DefinedProctimeAttribute {
   
       @Override
       public TypeInformation<Row> getReturnType() {
           String[] names = new String[] {"user_name" , "data"};
           TypeInformation[] types = new TypeInformation[] {Types.STRING(), Types.STRING()};
           return Types.ROW(names, types);
       }
   
       @Override
       public DataStream<Row> getDataStream(StreamExecutionEnvironment execEnv) {
           // create stream
           DataStream<Row> stream = ...;
           return stream;
       }
   
       @Override
       public String getProctimeAttribute() {
           // 这个名字的列会被追加到最后，作为第三列
           return "user_action_time";
       }
   }
   
   // register table source
   tEnv.registerTableSource("user_actions", new UserActionSource());
   
   WindowedTable windowedTable = tEnv
       .from("user_actions")
       .window(Tumble
           .over(lit(10).minutes())
           .on($("user_action_time"))
           .as("userActionWindow"));
   ```

### 事件时间

> 事件时间允许程序按照数据中包含的时间来处理，这样可以在有乱序或者晚到的数据的情况下产生一致的处理结果。为了能够处理乱序的事件，并且区分正常到达和晚到的事件，Flink 需要从事件中获取事件时间并且产生 [watermarks](https://nightlies.apache.org/flink/flink-docs-master/zh/docs/concepts/time/)。

定义事件时间的三种方法:

1. 在 DDL 中定义

   > WATERMARK 语句在一个已有字段上定义一个 watermark 生成表达式，同时标记这个已有字段为时间属性字段。

   Flink 支持和在 TIMESTAMP 列和 TIMESTAMP_LTZ 列上定义事件时间。

   - 如果源数据中的时间戳数据表示为年-月-日-时-分-秒，则通常为不带时区信息的字符串值，例如 `2020-04-15 20:13:40.564`，建议将事件时间属性定义在 `TIMESTAMP` 列上,

     ```java
     CREATE TABLE user_actions (
       user_name STRING,
       data STRING,
       user_action_time TIMESTAMP(3),
       -- 声明 user_action_time 是事件时间属性，并且用 延迟 5 秒的策略来生成 watermark
       WATERMARK FOR user_action_time AS user_action_time - INTERVAL '5' SECOND
     ) WITH (
       ...
     );
     
     SELECT TUMBLE_START(user_action_time, INTERVAL '10' MINUTE), COUNT(DISTINCT user_name)
     FROM user_actions
     GROUP BY TUMBLE(user_action_time, INTERVAL '10' MINUTE);
     ```

   - 如果源数据中的时间戳数据为带时区信息的字符串值，例如源数据中的时间戳数据表示为一个纪元 (epoch) 时间，通常是一个 long 值，例如 `1618989564564`，建议将事件时间属性定义在 `TIMESTAMP_LTZ` 列上：

     ```java
     CREATE TABLE user_actions (
      user_name STRING,
      data STRING,
      ts BIGINT,
      time_ltz AS TO_TIMESTAMP_LTZ(ts, 3),
      -- declare time_ltz as event time attribute and use 5 seconds delayed watermark strategy
      WATERMARK FOR time_ltz AS time_ltz - INTERVAL '5' SECOND
     ) WITH (
      ...
     );
     
     SELECT TUMBLE_START(time_ltz, INTERVAL '10' MINUTE), COUNT(DISTINCT user_name)
     FROM user_actions
     GROUP BY TUMBLE(time_ltz, INTERVAL '10' MINUTE);
     ```

2. 在 DataStream 到 Table 转换时定义

   > 事件时间属性可以用 `.rowtime` 后缀在定义 `DataStream` schema 的时候来定义。
   >
   > [时间戳和 watermark](https://nightlies.apache.org/flink/flink-docs-master/zh/docs/concepts/time/) 在这之前一定是在 `DataStream` 上已经定义好了。 在从 DataStream 转换到 Table 时，由于 `DataStream` 没有时区概念，因此 Flink 总是将 `rowtime` 属性解析成 `TIMESTAMP WITHOUT TIME ZONE` 类型，并且将所有事件时间的值都视为 UTC 时区的值。

   - Option 1

   ```java
   // 基于 stream 中的事件产生时间戳和 watermark
   DataStream<Tuple2<String, String>> stream = inputStream.assignTimestampsAndWatermarks(...);
   
   // 声明一个额外的逻辑字段作为事件时间属性
   Table table = tEnv.fromDataStream(stream, $("user_name"), $("data"), $("user_action_time").rowtime());
   ```

   - Option 2

   ```java
     // 从第一个字段获取事件时间，并且产生 watermark
   DataStream<Tuple3<Long, String, String>> stream = inputStream.assignTimestampsAndWatermarks(...);
   
   // 第一个字段已经用作事件时间抽取了，不用再用一个新字段来表示事件时间了
   Table table = tEnv.fromDataStream(stream, $("user_action_time").rowtime(), $("user_name"), $("data"));
   
   // Usage:
   
   WindowedTable windowedTable = table.window(Tumble
         .over(lit(10).minutes())
         .on($("user_action_time"))
         .as("userActionWindow"));
   ```

3. 使用 TableSource 定义

   > 事件时间属性可以在实现了 `DefinedRowTimeAttributes` 的 `TableSource` 中定义。`getRowtimeAttributeDescriptors()` 方法返回 `RowtimeAttributeDescriptor` 的列表，包含了描述事件时间属性的字段名字、如何计算事件时间、以及 watermark 生成策略等信息。
   >
   > 同时需要确保 `getDataStream` 返回的 `DataStream` 已经定义好了时间属性。
   >
   > 只有在定义了 `StreamRecordTimestamp` 时间戳分配器的时候，才认为 `DataStream` 是有时间戳信息的。 只有定义了 `PreserveWatermarks` watermark 生成策略的 `DataStream` 的 watermark 才会被保留。反之，则只有时间字段的值是生效的。

   ```java
   // 定义一个有事件时间属性的 table source
   public class UserActionSource implements StreamTableSource<Row>, DefinedRowtimeAttributes {
   
       @Override
       public TypeInformation<Row> getReturnType() {
           String[] names = new String[] {"user_name", "data", "user_action_time"};
           TypeInformation[] types =
               new TypeInformation[] {Types.STRING(), Types.STRING(), Types.LONG()};
           return Types.ROW(names, types);
       }
   
       @Override
       public DataStream<Row> getDataStream(StreamExecutionEnvironment execEnv) {
           // 构造 DataStream
           // ...
           // 基于 "user_action_time" 定义 watermark
           DataStream<Row> stream = inputStream.assignTimestampsAndWatermarks(...);
           return stream;
       }
   
       @Override
       public List<RowtimeAttributeDescriptor> getRowtimeAttributeDescriptors() {
           // 标记 "user_action_time" 字段是事件时间字段
           // 给 "user_action_time" 构造一个时间属性描述符
           RowtimeAttributeDescriptor rowtimeAttrDescr = new RowtimeAttributeDescriptor(
               "user_action_time",
               new ExistingField("user_action_time"),
               new AscendingTimestamps());
           List<RowtimeAttributeDescriptor> listRowtimeAttrDescr = Collections.singletonList(rowtimeAttrDescr);
           return listRowtimeAttrDescr;
       }
   }
   
   // register the table source
   tEnv.registerTableSource("user_actions", new UserActionSource());
   
   WindowedTable windowedTable = tEnv
       .from("user_actions")
       .window(Tumble.over(lit(10).minutes()).on($("user_action_time")).as("userActionWindow"));
   ```

---

### format

- timestamp

  可以将时间戳类型数据最高精确微秒(百万分之一秒)，数据类型定义为timestamp(N),N取值范围为0-6，默认为0，如需要精确到毫秒则设置为Timestamp(3)，如需要精确到微秒则设置为timestamp(6)，数据精度提高的代价是其内部存储空间的变大，但仍未改变时间戳类型的最小和最大取值范围。

### connector kafka

# deduplication

- 语法

  ```sql
  SELECT [column_list]
  
  FROM (
  
     SELECT [column_list],
  
       ROW_NUMBER() OVER ([PARTITION BY col1[, col2...]]
  
         ORDER BY col1 [asc|desc][, col2 [asc|desc]...]) AS rownum
  
     FROM table_name)
  
  WHERE rownum <= N [AND conditions]
  ```

  | 参数                               | 说明                                                         |
  | ---------------------------------- | ------------------------------------------------------------ |
  | ROW_NUMBER()                       | 计算行号的OVER窗口函数。行号从1开始计算。                    |
  | PARTITION BY col1[, col2..]        | 可选。指定分区的列，即去重的KEYS。                           |
  | ORDER BY timeAttributeCol asc desc | 指定排序的列，必须是一个的字段（即Proctime或Rowtime）。可以指定顺序（Keep FirstRow）或者倒序 （Keep LastRow）。 |
  | rownum                             | 外层查询中对排名进行过滤，只取前N条                          |

  - Deduplicate Keep FirstRow保留首行的去重策略：保留KEY下第一条出现的数据，之后出现该KEY下的数据会被丢弃掉。因为STATE中只存储了KEY数据，所以性能较优。
  - Deduplicate Keep LastRow保留末行的去重策略：保留KEY下最后一条出现的数据。因此过程中会产生变更的记录，会向下游发送变更的消息。因此，sink表需要支持retract操作。

在 Deduplication 关于是否会出现回撤流

1. ⭐ Order by 事件时间 DESC：会出现回撤流，因为当前 key 下 `可能会有` 比当前事件时间还大的数据
2. ⭐ Order by 事件时间 ASC：会出现回撤流，因为当前 key 下 `可能会有` 比当前事件时间还小的数据
3. ⭐ Order by 处理时间 DESC：会出现回撤流，因为当前 key 下 `可能会有` 比当前处理时间还大的数据
4. ⭐ Order by 处理时间 ASC：不会出现回撤流，因为当前 key 下 `不可能会有` 比当前处理时间还小的数据

---

reference

https://www.modb.pro/db/232004



# join

> 使用 SQL 进行数据分析的过程中，join 是经常要使用的操作。
>
> 在离线场景中，join 的数据集是有边界的，可以缓存数据有边界的数据集进行查询，有Nested Loop/Hash Join/Sort Merge Join 等多表 join；
>
> 而在实时场景中，join 两侧的数据都是无边界的数据流，所以缓存数据集对长时间 job 来说，存储和查询压力很大，另外双流的到达时间可能不一致，造成 join 计算结果准确度不够；因此，Flink SQL 提供了多种 join 方法，来帮助用户应对各种 join 场景。

### regular join

> regular join 是最通用的 join 类型，不支持时间窗口以及时间属性，任何一侧数据流有更改都是可见的，直接影响整个 join 结果。如果有一侧数据流增加一个新纪录，那么它将会把另一侧的所有的过去和将来的数据合并在一起，因为 regular join 没有剔除策略，这就影响最新输出的结果; 正因为历史数据不会被清理，所以 regular join 支持数据流的任何更新操作。
>
> 对于 regular join 来说，更适合用于离线场景和小数据量场景。

- 语法

  ```sql
  SELECT columns
  FROM t1  [AS <alias1>]
  [LEFT/INNER/FULL OUTER] JOIN t2
  ON t1.column1 = t2.key-name1
  ```

### interval join

> 相对于 regular join，interval Join 则利用窗口的给两个输入表设定一个 Join 的时间界限，超出时间范围的数据则对 join 不可见并可以被清理掉，这样就能修正 regular join 因为没有剔除数据策略带来 join 结果的误差以及需要大量的资源。
>
> 但是使用interval join，需要定义好时间属性字段，可以是计算发生的 Processing Time，也可以是根据数据本身提取的 Event Time；如果是定义的是 Processing Time，则Flink 框架本身根据系统划分的时间窗口定时清理数据；如果定义的是 Event Time，Flink 框架分配 Event Time 窗口并根据设置的 watermark 来清理数据。

- 语法1

  ```sql
  SELECT columns
  FROM t1  [AS <alias1>]
  [LEFT/INNER/FULL OUTER] JOIN t2
  ON t1.column1 = t2.key-name1 AND t1.timestamp BETWEEN t2.timestamp  AND  BETWEEN t2.timestamp + + INTERVAL '10' MINUTE;
  ```

- 语法2

  ```sql
  SELECT columns
  FROM t1  [AS <alias1>]
  [LEFT/INNER/FULL OUTER] JOIN t2
  ON t1.column1 = t2.key-name1 AND t2.timestamp <= t1.timestamp and t1.timestamp <=  t2.timestamp + + INTERVAL ’10' MINUTE ;
  ```

### temproal table join

> interval Join 提供了剔除数据的策略，解决资源问题以及计算更加准确，这是有个前提：join 的两个流需要时间属性，需要明确时间的下界，来方便剔除数据；
>
> 显然，这种场景不适合维度表的 join，因为维度表没有时间界限，对于这种场景，Flink 提供了 temproal table join 来覆盖此类场景。
>
> 在 regular join和interval join中，join 两侧的表是平等的，任意的一个表的更新，都会去和另外的历史纪录进行匹配，temproal table 的更新对另一表在该时间节点以前的记录是不可见的。

- 语法

  ```sql
  SELECT columns
  FROM t1  [AS <alias1>]
  [LEFT] JOIN t2 FOR SYSTEM_TIME AS OF t1.proctime [AS <alias2>]
  ON t1.column1 = t2.key-name1
  ```

### lookup join

- 维表必须指定主键。维表JOIN时，ON的条件必须包含所有主键的等值条件


---

reference:

[Flink SQL 实战：双流 join 场景应用-阿里云开发者社区](https://developer.aliyun.com/article/780048?accounttraceid=dd5fdbf3eed04f6185ed6461d8a33012zihq)

[Flink SQL 功能解密系列 —— 维表 JOIN 与异步优化](https://www.liangzl.com/get-article-detail-114889.html)

# watermark

Watermark是Apache Flink为了处理EventTime 窗口计算提出的一种机制,本质上也是一种时间戳。

由Apache Flink Source或者自定义的Watermark生成器按照需求Punctuated或者Periodic两种方式生成的一种系统Event，与普通数据流Event一样流转到对应的下游算子，接收到Watermark Event的算子以此不断调整自己管理的EventTime clock。

Apache Flink 框架保证Watermark单调递增，算子接收到一个Watermark时候，框架知道不会再有任何小于该Watermark的时间戳的数据元素到来了，所以Watermark可以看做是告诉Apache Flink框架数据流已经处理到什么位置(时间维度)的方式。

Watermark的产生和Apache Flink内部处理逻辑如下图所示:

![](/images/flink/flink_watermark.png)

### 产生方式

- Punctuated - 数据流中每一个递增的EventTime都会产生一个Watermark。 在实际的生产中Punctuated方式在TPS很高的场景下会产生大量的Watermark在一定程度上对下游算子造成压力，所以只有在实时性要求非常高的场景才会选择Punctuated的方式进行Watermark的生成。

- Periodic - 周期性的（一定时间间隔或者达到一定的记录条数）产生一个Watermark。在实际的生产中Periodic的方式必须结合时间和积累条数两个维度继续周期性产生Watermark，否则在极端情况下会有很大的延时。

# upsert kafka

The Kafka connector in Flink SQL can work in two streaming modes. 

**Upsert mode** allows us to get the latest value for a specific entity automatically without any manual deduplication. One of the typical scenarios where you can leverage this mode is a SQL join of two tables, where one of the tables is keeping history of changes per some entity id. Once you join on such an entity id which is non-unique by design, you get unwanted rows, but you usually want to see the latest value of that entity. With upsert mode, Flink automatically normalizes before the tables are joined. Eventually it allows you to easily answer typical business questions on getting a real-time view of the shared resources like cars, planes, workers, etc.

**Append mode** is still an option to go with, if a business query does not need to filter out all historical events, but rather show the history of changes at the end. In this scenario, query may run faster with append mode, as Flink does not need to do any changelog normalization.

## tips

- upsert-kafka 可以配合 checkpoint使用，这样不需要再从头消费之前的数据

- 如果kafka中key没有值，是不符合upsert-kafka的格式的，flink sql引用此类topic为upsert-kafka，则不会触发计算

## deduplication

```sql
SELECT [column_list]
FROM (
   SELECT [column_list],
     ROW_NUMBER() OVER ([PARTITION BY col1[, col2...]]
       ORDER BY time_attr [asc|desc]) AS rownum
   FROM table_name)
WHERE rownum = 1
```

- 注意 在内嵌的 select语句中，建议使用 column_list 查询指定的字段，不要使用 * 代指所有指标。否则查询upsert-kafka会，一直从最新的记录读取。
