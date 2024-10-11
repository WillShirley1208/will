---
title: spring snippet
date: 2016-10-16 16:01:30
tags: snippet
categories: spring
---

# 知识点

## 传JSON字符串+@RequestBody接收

前端

```java
function icheckDelete(url){
  var parms = {
      list : array //这是个数组
  };

  $.ajax({
      dataType: "JSON",
      contentType:'application/json;charset=UTF-8',//关键是要加上这行
      traditional:true,//这使json格式的字符不会被转码
      data: JSON.stringify(parms),
      type: "DELETE", 
      timeout: 20000,
      url: url,
      success : function () {
          alert("删除成功！");
      },
      error : function (data){
          alert(data.responseText);
      }
  });
```

}

```java
- 后端
Controller:

@RequestMapping(value = "deleteList", method = RequestMethod.DELETE)
@ResponseBody
public String delete(@RequestBody DeleteListRequest request) throws WebTransException{

    /* 逻辑代码 */

    return "success";
}
```

## RESTFUL

-  `@RestController` class level annotation and then add the following `@RequestMapping` to the class

# springboot

## 配置文件

- ```
  bootstrap.yml（bootstrap.properties）用来程序引导时执行，应用于更加早期配置信息读取，如可以使用来配置application.yml中使用到参数等
  
  application.yml（application.properties) 应用程序特有配置信息，可以用来配置后续各个模块中需使用的公共参数等。
  
  加载顺序：bootstrap.yml > application.yml > application-dev(prod).yml 
  ```

  

## 问题随记

### Spring Boot通过Mybatis，使用mapper接口和xml配置sql，连接数据库

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