---
title: flink table
date: 2021-12-24 09:00:00
tags: learn
categories: flink
---

## 时间属性

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
   >  只有在定义了 `StreamRecordTimestamp` 时间戳分配器的时候，才认为 `DataStream` 是有时间戳信息的。 只有定义了 `PreserveWatermarks` watermark 生成策略的 `DataStream` 的 watermark 才会被保留。反之，则只有时间字段的值是生效的。

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

