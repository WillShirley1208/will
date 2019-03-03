---
title: mybatis snippet
date: 2016-10-16 12:01:30
tags: snippet
categories: mybatis
---

### 知识点

- about date jdbcType

  ```
  #{beginDate,jdbcType=DATE} (no time of the day), or
  #{beginDate,jdbcType=TIMESTAMP} (if you need to include the time of the day).
  ```

And the Java parameter you want to apply should be of type:
  ```
  java.sql.Date (date without time)
  java.sql.Timestamp (timestamp)
  java.util.Date (date and time).
  ```

- the Greater Than/Equal Operator and the Less Than/Equal Operators:
  ```
  ROWNUM &gt;= 20
  ROWNUM &lt;= 20
  ```

### 问题解决

使用SpringMVC+Mybatis框架写项目的时候，在mapper里面的sql语句

```xml
	<if test="updateTime != null">
		and update_time &gt; #{updateTime,jdbcType=TIMESTAMP}
	</if>
```
出错：`There is no getter for property named 'updateTime' in 'class java.util.Date'`

原因是：
如果将and语句中的#｛属性｝写在if语句中，mybatis会去对象中从它的get方法中取值，而我之前是直接传递的值，并没有封装对象。
解决办法：

 	1. 把updateTime需改为date
	2. 把updateTime代表的参数封装到一个对象中
