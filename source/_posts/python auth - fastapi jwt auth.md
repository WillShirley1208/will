---
title: python auth - fastapi jwt auth
date: 2023-12-15 10:46:58
tags: auth
categories: python
---

在 FastAPI JWT Auth 中，Refresh Token（刷新令牌）和 Access Token（访问令牌）是两种不同的令牌，用于实现认证和授权机制。

1. Access Token（访问令牌）：Access Token 是一个短期的令牌，用于验证用户的身份并授权用户访问受保护的资源。Access Token 是通过用户提供的凭证（如用户名和密码）进行身份验证后生成的，通常具有较短的有效期（例如几分钟或几小时）。客户端在每次请求受保护的资源时，需要在请求的头部或请求参数中携带 Access Token，以证明其有权访问该资源。
2. Refresh Token（刷新令牌）：Refresh Token 是一个长期有效的令牌，用于在 Access Token 过期后重新获取新的 Access Token。当 Access Token 过期时，客户端可以使用 Refresh Token 向服务器发送请求来获取新的 Access Token，而无需重新提供用户名和密码等凭证。通常情况下，Refresh Token 的有效期要比 Access Token 更长。

使用 Refresh Token 的主要目的是增强安全性和减少频繁的身份验证操作。通过将 Access Token 的有效期设置较短，可以降低令牌泄露的风险。当 Access Token 过期时，使用 Refresh Token 获取新的 Access Token，这样可以减少频繁地要求用户重新提供凭证的情况。

一般的流程是，客户端在进行身份验证后会收到一个 Access Token 和一个 Refresh Token。客户端使用 Access Token 来访问受保护的资源，当 Access Token 过期时，客户端使用 Refresh Token 向服务器请求新的 Access Token。

FastAPI JWT Auth 提供了相应的功能和装饰器来处理 Access Token 和 Refresh Token 的生成、验证和刷新等操作，以方便实现 JWT 认证和授权。



refresh tokens **cannot access** an endpoint that is protected with **jwt_required()**, **jwt_optional()**, and **fresh_jwt_required()** 

access tokens **cannot access** an endpoint that is protected with **jwt_refresh_token_required()**.



## Refresh Tokens

These are long-lived tokens which can be used to create a new access tokens once an old access token has expired. 

