---
title: oauth2 practise
date: 2024-05-31 18:04:12
tags: pracise
categories: oauth2
---

## overview

### OAuth 2.0 terminology

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