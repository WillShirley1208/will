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

## 返回码


RESTful API 中的标准返回码通常遵循 HTTP 状态码，并根据不同的操作和结果返回不同的状态码。以下是一些常见的 HTTP 状态码及其通常的含义：

- **200 OK**: 表示请求成功。通常用于 GET 请求。
- **201 Created**: 表示成功创建了新的资源。通常用于 POST 请求。
- **204 No Content**: 表示请求成功处理，但没有返回内容。通常用于 DELETE 请求。
- **400 Bad Request**: 表示客户端发送的请求有错误，服务器无法理解。
- **401 Unauthorized**: 表示请求需要用户认证，用户没有提供有效的认证信息。
- **403 Forbidden**: 表示服务器理解请求，但拒绝执行，通常因为权限问题。
- **404 Not Found**: 表示请求的资源不存在。
- **405 Method Not Allowed**: 表示请求方法不被允许。
- **409 Conflict**: 表示请求可能引发冲突，例如更新资源时的版本冲突。
- **500 Internal Server Error**: 表示服务器在处理请求时发生了错误。
- 502 [Bad Gateway](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/502) - This indicates an invalid response from an upstream server.
- 503 Service Unavailable - This indicates that something unexpected happened on server side (It can be anything like server overload, some parts of the system failed, etc.).

RESTful API 的返回码可以根据实际情况进行适当扩展和调整，但通常遵循以上基本的 HTTP 状态码标准。



## TIPS

- url中包含传参时，例如delete  /auth/user/{参数}，那此时其他delete方法 /auth/user/other，会发生冲突，需要在路径侯后面添加“/”，变成/auth/user/other/



---

- reference [Best practices for REST API design](https://stackoverflow.blog/2020/03/02/best-practices-for-rest-api-design/)