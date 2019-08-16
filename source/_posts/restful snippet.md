---
title: restful snippet
date: 2017-12-08 12:01:30
tags: snippet
categories: restful
---

- **GET**: A safe read-only method that reads a single or a list of resources.
- **POST**: Creates a new resource.
- **PUT**: Completely replaces the resource(s) at the given location with the new data.
- **PATCH**: Merges the resource(s) at the given location with the new data.
- **DELETE**: Deletes the resource(s) at a location.
- **HEAD**: Same as GET but only returns the header and no data.

**用 URL 表示要操作的资源，用不同的 HTTP 请求（GET，POST，PUT，DELETE）描述对资源的操作，通过 HTTP 的状态码来判断此次对资源操作的结果**，这就是 Restful风格。 

1. GET 用来获取资源
2. POST 用来新增资源
3. PUT 用来更新资源
4. DELETE 用来删除资源

- put和delete虽然是http协议的规范 ,但是遗憾的是,所有的无论是html4还是h5都不支持,所以我们在实际开开发过程当中要模拟这两种状态。 


### Restful有什么意义

- 网络上的所有的信息体都看作一种资源，对网络资源的某种操作，都是通过 method 来确定的。 
- 对于现在的数据或者资源提供方，对外透露的接口一般是 restful 风格的，有利于不同系统之间的资源共享,而且只需要遵守规范不需要做另外的配置就能达到资源共享。 

**restful 是用来外提供接口的，主要用于在不同的系统之间进行数据的交互。**

**ps：其实在做内部系统的时候并不会使用 restful 风格的 url 请求，只用普通的 @RequestMapping 来匹配请求就行了。**