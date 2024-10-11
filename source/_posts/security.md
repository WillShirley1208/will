---
title: security
date: 2023-12-16 10:57:00
tags: tools
categories: security
---

# security model

- [Access control list](https://en.wikipedia.org/wiki/Access_control_list) (ACL)

- [Attribute-based access control](https://en.wikipedia.org/wiki/Attribute-based_access_control) (ABAC)

- [Bell–LaPadula model](https://en.wikipedia.org/wiki/Bell–LaPadula_model)

- [Biba model](https://en.wikipedia.org/wiki/Biba_model)

- [Brewer and Nash model](https://en.wikipedia.org/wiki/Brewer_and_Nash_model)

- [Capability-based security](https://en.wikipedia.org/wiki/Capability-based_security)

- [Clark-Wilson model](https://en.wikipedia.org/wiki/Clark-Wilson_model)

- [Context-based access control](https://en.wikipedia.org/wiki/Context-based_access_control) (CBAC)

- [Graham-Denning model](https://en.wikipedia.org/wiki/Graham-Denning_model)

- [Harrison-Ruzzo-Ullman](https://en.wikipedia.org/wiki/HRU_(security)) (HRU)

- [High-water mark (computer security)](https://en.wikipedia.org/wiki/High-water_mark_(computer_security))

- [Lattice-based access control](https://en.wikipedia.org/wiki/Lattice-based_access_control) (LBAC)

- [Mandatory access control](https://en.wikipedia.org/wiki/Mandatory_access_control) (MAC)

- [Multi-level security](https://en.wikipedia.org/wiki/Multi-level_security) (MLS)

- [Non-interference (security)](https://en.wikipedia.org/wiki/Non-interference_(security))

- [Object-capability model](https://en.wikipedia.org/wiki/Object-capability_model)

- [Protection ring](https://en.wikipedia.org/wiki/Protection_ring)

- [Role-based access control](https://en.wikipedia.org/wiki/Role-based_access_control) (RBAC)

  ![](/images/rbac/rbac_auth.png)

  reference

  - [知乎](https://zhuanlan.zhihu.com/p/63769951)
  - [CSDN](https://blog.csdn.net/ljw499356212/article/details/81055141)

  - https://benheart.github.io/2016/11/19/rbac-design-doc.html

  

- [Take-grant protection model](https://en.wikipedia.org/wiki/Take-grant_protection_model)

- [Discretionary access control](https://en.wikipedia.org/wiki/Discretionary_access_control) (DAC)

>  SSO (single sign-on)

> https://ninjaauth.com/sso-single-sign-on

# oauth2

## OAuth 2.0 terminology

- **Resource Owner**: Entity that can grant access to a protected resource. Typically, this is the end-user.
- **Client**: Application requesting access to a protected resource on behalf of the Resource Owner.
- **Resource Server**: Server hosting the protected resources. This is the API you want to access.
- **Authorization Server**: Server that authenticates the Resource Owner and issues Access Tokens after getting proper authorization. In this case, Auth0.
- **User Agent**: Agent used by the Resource Owner to interact with the Client (for example, a browser or a native application).

![user-journey-oauth](/images/security/user-journey-oauth.webp)



The best practice for implementing OAuth2 in a web application with a separate frontend and backend is to use the Backend for Frontend (BFF) pattern. Here's how it works:

The frontend (e.g. a Single Page Application) initiates the OAuth2 flow by redirecting the user to the OAuth2 provider's authorization endpoint. The provider then authenticates the user and redirects back to the frontend with an authorization code.

The frontend then sends this authorization code to the backend over a secure channel (e.g. HTTPS). The backend acts as a confidential client and exchanges the authorization code with the OAuth2 provider for an access token and refresh token.[1][3]

The backend stores the tokens securely and returns an opaque session cookie or token to the frontend. This session identifier represents the authenticated user session.[1]

For subsequent API requests, the frontend includes the session cookie/token. The backend validates the session and retrieves the access token, using it to authenticate the request to the backend API or other third-party APIs on behalf of the user.[1][3]

This pattern has several advantages:

1. The access token is never exposed to the frontend, mitigating the risk of token theft.[1][3]

2. The backend can securely store and manage the refresh of access tokens without involving the frontend.[3]

3. The backend can act as a gatekeeper, validating requests and enforcing authorization rules before proxying to third-party APIs.[3]

4. The frontend only needs to handle the initial OAuth2 redirect and session management, simplifying its code.[1]

To implement this, the backend must be registered as a confidential OAuth2 client with the provider. The frontend can use libraries like Auth0 or Google's client-side libraries to initiate the OAuth2 flow.[1][4] The backend handles the code exchange and token management, often using server-side OAuth2 libraries or middleware.[1]

Citations:
[1] https://auth0.com/blog/backend-for-frontend-pattern-with-auth0-and-dotnet/
[2] https://community.qlik.com/t5/New-to-Qlik-Analytics/How-to-Secure-Backend-API-Endpoints-on-the-Front-End-Best/td-p/2423853
[3] https://auth0.com/blog/the-backend-for-frontend-pattern-bff/
[4] https://infisical.com/blog/guide-to-implementing-oauth2
[5] https://www.reddit.com/r/node/comments/skmjjn/0auth_implemented_on_frontend_or_backend/



----

reference

- https://auth0.com/docs/get-started/authentication-and-authorization-flow/which-oauth-2-0-flow-should-i-use

# keycloak

### 前提

- 需要jdk9以上环境



### config server

- 编辑 `/conf/keycloak.conf `，配置mysql连接信息 (mysql创建keycloak库)

- 配置 `admin cli `环境变量 （方便直接命令操作，等同于界面操作）

  ```shell
  # keycloak
  export KCADM="/Users/dongwei/Applications/keycloak-23.0.3/bin/kcadm.sh"
  export HOST_FOR_KCADM=localhost
  ```



### bootstrap

- 首次启动

  `KEYCLOAK_ADMIN=admin KEYCLOAK_ADMIN_PASSWORD=xxxxxx ./bin/kc.sh start-dev`

- 后续启动

  `./bin/kc.sh start-dev`

- 生效 `KCADM`

  `$KCADM config credentials --server http://localhost:8080 --realm master --user admin`

  提示输入密码即可 (1sou2sou)

​		验证 `$KCADM get serverinfo`





### config realm

- 创建新的realm

  `$KCADM create realms -s realm="jarvex" -s enabled=true`



### config clients

```shell
$KCADM create clients -r jarvex -s clientId="jarvex-client-test-1" -s enabled=true -s name="test Client 1" -s protocol=openid-connect -s publicClient=true -s standardFlowEnabled=true -s 'redirectUris=["http://127.0.0.1:8001/app/*"]' -s baseUrl="http://127.0.0.1:8001/app/" -s 'webOrigins=["*"]'
```

# shiro

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
