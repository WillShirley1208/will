---
title: shiro snippet
date: 2016-05-20 21:50:30
tags: snippet
categories: shiro
---

**对于一个好的框架，从外部来看应该具有非常简单易于使用的 API，且 API 契约明确；从内部来看的话，其应该有一个可扩展的架构，即非常容易插入用户自定义实现，因为任何框架都不能满足所有需求。 **

------



- Subject

  主体，代表了当前的用户

- SecurityManager

  安全管理器，它管理着所有Subject，相当于springmvc中的DispatcherServlet(前端控制器)

- Realm

  域，Shiro从Realm获取安全数据（如用户、角色、权限） ，是验证主体subject的数据源

流程如图：

![](http://wiki.jikexueyuan.com/project/shiro/images/2.png)

**流程说明**：

1. 应用代码通过 Subject 来进行认证和授权，而 Subject 又委托给 SecurityManager；
2. 我们需要给 Shiro 的 SecurityManager 注入 Realm，从而让 SecurityManager 能得到合法的用户及其权限进行判断。

**从以上也可以看出，Shiro 不提供维护用户 / 权限，而是通过 Realm 让开发人员自己注入。** 

------

shiro的内部结构如图：

![](http://wiki.jikexueyuan.com/project/shiro/images/3.png)

------

### 身份验证

**principals**：身份，即主体的标识属性，可以是任何东西，如用户名、邮箱等，唯一即可。 

**credentials**：证明 / 凭证，即只有主体知道的安全值，如密码 / 数字证书等。 

- 身份认证流程

![](http://wiki.jikexueyuan.com/project/shiro/images/4.png)

------

### 授权

授权，也叫访问控制，即在应用中控制谁能访问哪些资源（如访问页面/编辑数据/页面操作等）。在授权中需了解的几个关键对象：主体（Subject）、资源（Resource）、权限（Permission）、角色（Role）。 

- 授权方式

  编程式、注解式、jsp/gsp标签式