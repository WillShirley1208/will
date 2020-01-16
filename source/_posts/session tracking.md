---
title: session tracking
date: 2015-02-18 18:01:30
tags: learn
categories: java
---

## session

>Session simply means a particular interval of time.
>Session Tracking is a way to maintain state (data) of an user. It is also known as session management in servlet.

- HTTP is stateless that means each request is considered as the new request. It is shown in the figure given below:
  ![](https://www.javatpoint.com/images/newrequest.JPG)

- There are four techniques used in Session tracking:
  - Cookies
  - Hidden Form Field
  - URL Rewriting
  - HttpSession

## cookie

>A cookie is a small piece of information that is persisted between the multiple client requests.
>A cookie has a name, a single value, and optional attributes such as a comment, path and domain qualifiers, a maximum age, and a version number.

### how cookie work

![](https://www.javatpoint.com/phppages/images/cookie.png)

### type of cookie

- Non-persistent cookie

  >It is valid for single session only. It is removed each time when user closes the browser.
- Persistent cookie

  >It is valid for multiple session . It is not removed each time when user closes the browser. It is removed only if user logout or signout.

- Advantage 
  - Simplest technique of maintaining the state.
  - Cookies are maintained at client side.
- Disadvantage
  - It will not work if cookie is disabled from the browser.
  - Only textual information can be set in Cookie object.

## HttpSession Interface

- An object of HttpSession can be used to perform two tasks:
  - bind objects
  - view and manipulate information about a session, such as the session identifier, creation time, and last accessed time.
    ![](https://www.javatpoint.com/images/httpsession.JPG)