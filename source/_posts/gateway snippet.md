---
title: gateway snippet
date: 2023-11-05 16:44:45
tags: snippet
categories: gateway
---

# 概念

- LB， "Load Balancing"（负载均衡）的缩写。负载均衡是一种分布式系统中常见的策略，用于将请求均匀地分发到多个服务实例上，以避免某个实例负载过重，提高整体系统的性能、可靠性和可用性。
- restful header中的所有key，对应的值其实都是集合，只是大多数情况下集合里面只有一个元素

### spring cloud gateway

- `DefaultErrorWebExceptionHandler` 是 Spring Web 的默认实现类，实现了 `ErrorWebExceptionHandler` 接口。它提供了默认的错误处理行为，当没有自定义的 `ErrorWebExceptionHandler` 实现时，Spring Boot 将使用 `DefaultErrorWebExceptionHandler` 来处理错误。
- **原生的spring cloud gateway进行请求转发，所有的微服务实例与gateway服务实例在nacos中必须是同一个namesapce和同一个group。**

# 静态路由

- 使用spring cloud gateway通过代码方式或配置文件方式实现

# 动态路由

## spring cloud gateway 与 nacos结合实现

> 实现思路：使用nacos的配置中心，将路由配置放在nacos上，写个监听器监听nacos上配置的变化，将变化后的配置更新到GateWay应用的进程内。

- 场景一（基础）

  可以先在nacos的配置管理中，添加微服务的配置信息（端口、路由等），然后启动微服务程序（不用配置相关端口配置）

- 场景二（predicate）

  可以控制请求的cookie、header、host、method、path、query、RemoteAddr、Weight（按照权重将请求分发到不同节点服务）

- 场景三（filters）

  可以控制请求的AddRequestHeader、AddResponseHeader、DedupeResponseHeader、DedupeResponseHeader、CircuitBreaker（断路器）、FallbackHeaders、PrefixPath、PreserveHostHeader（加上请求者的ip信息）、RequestRateLimiter（限流）、RedirectTo、RemoveRequestHeader、RemoveResponseHeader、RewritePath（将请求参数中的路径做变换）、RewriteLocationResponseHeader、RewriteResponseHeader、SecureHeaders、SetPath、SetRequestHeader、SetResponseHeader、SetStatus、StripPrefix、Retry、RequestSize、SetRequestHostHeader、ModifyRequestBody、ModifyResponseBody、TokenRelay（配合鉴权）

- 场景四（限流）
  - 请求同一目标地址

- 场景五（修改请求和响应body）
- 场景六（地址分发）
  - 有多个后端应用服务



---

## 注意

- spring cloud gateway中如果配置文件使用lb访问服务，那需要引入lb相关依赖包

  ```xml
  <dependency>
  	<groupId>org.springframework.cloud</groupId>
  	<artifactId>spring-cloud-starter-loadbalancer</artifactId>
  </dependency>
  ```

---

## 参考

- [官网]( https://cloud.spring.io/spring-cloud-gateway/reference/html/#configuring-route-predicate-factories-and-gateway-filter-factories)
- [程序员欣宸](https://blog.csdn.net/boling_cavalry/category_9278131.html)

- 其它
  - https://blog.csdn.net/abu935009066/article/details/112245330