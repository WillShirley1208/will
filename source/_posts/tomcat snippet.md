---
title: tomcat snippet
date: 2016-12-16 12:01:30
tags: snippet
categories: tomcat
---

### 配置项

- 项目部署到linux的tomcat中，验证码在页面中刷新不了，需要修改`catalina.sh`,添加`JAVA_OPTS="$JAVA_OPTS -Djava.awt.headless=true"`配置

### 设置虚拟路径的两种方法

- 使用tomcat自身设置虚拟路径

1. 在tomcat的server.xml配置文件中，配置context标签

   Context标签中的相关属性：

   - path: 指定访问该web应用的URL入口，即虚拟文件目录
   - docBase:为访问的真实路径
   - reloadable:如果这个属性设为true，tomcat服务器在运行状态下会监视在WEB-INF/classes和WEB-INF/lib目录下class文件的改动， 如果监测到有class文件被更新的，服务器会自动重新加载Web应用。 

2. 如果这事使用idea启动项目，需要将Deploy applications configured in Tomcat instance勾上 


- 单独设置IDEA进行设置虚拟路径

  1. 上图的中`Deploy applications configured in Tomcat instance`不要勾上 

  2. 选中`edit configurations`，点击加号按钮，选择实际路径，并在`Appication context`中填写虚拟路径。

### 设置URL请求参数长度

- 问题描述

  post请求，本身请求参数无大小限制，但是一般的服务器像tomcat都有默认大小，如果传输的参数超过了默认大小，则会报错`http post request header is too large`

- 解决办法

  在server.xml中`<Connector connectionTimeout="20000" port="8080" protocol="HTTP/1.1" redirectPort="8443"/>  `，添加设置项`maxHttpHeaderSize ="102400" `