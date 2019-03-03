---
title: springboot snippet
date: 2016-08-14 18:38:22
tags: snippet
categories: springboot
---

## 问题随记

1. Spring Boot通过Mybatis，使用mapper接口和xml配置sql，连接数据库

- 首先在配置文件application.properties

  ```properties
  spring.datasource.url=jdbc:mysql://localhost:3306/test
  spring.datasource.username=***
  spring.datasource.password=***
  spring.datasource.driverClassName=com.mysql.jdbc.Driver
  
  #mybatis.typeAliasesPackage：为实体对象所在的包，跟数据库表一一对应
  #mybatis.mapperLocations：mapper文件的位置
  
  mybatis.typeAliasesPackage=com.xxx.model
  mybatis.mapperLocations=classpath:mybatis/mapper/*Mapper.xml
  ```

- 在启动类Application.java中写上配置信息 

  ```java
  @SpringBootApplication
  @MapperScan("com.xxx.dao") // mybatis扫描路径，针对的是接口Mapper类
  public class DailyreportApplication{
  
      public static void main(String[] args) {
          SpringApplication.run(DailyreportApplication.class, args);
      }
  }
  ```

  

2. mysql连接异常`java.sql.SQLException: Value '0000-00-00' can not be represented as java.sql.Date`

- 解决办法

  `jdbc:mysql://yourserver:3306/yourdatabase?zeroDateTimeBehavior=convertToNull `

  