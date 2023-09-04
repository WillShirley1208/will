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
