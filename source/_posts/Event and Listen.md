---
title: Event and Listen
date: 2016-03-1 09:00:30
tags: learn
categories: java
---

>Events are basically occurrence of something. Changing the state of an object is known as an event.
```
We can perform some important tasks at the occurrence of these exceptions, such as counting total and current logged-in users, creating tables of the database at time of deploying the project, creating database connection object etc.
```
### Event classes

- ServletRequestEvent
- ServletContextEvent
- ServletRequestAttributeEvent
- ServletContextAttributeEvent
- HttpSessionEvent
- HttpSessionBindingEvent

### Event interfaces

- ServletRequestListener
- ServletRequestAttributeListener
- ServletContextListener
- ServletContextAttributeListener
- HttpSessionListener
- HttpSessionAttributeListener
- HttpSessionBindingListener
- HttpSessionActivationListener

---

### ServletContextEvent and ServletContextListener

- The ServletContextEvent is notified when web application is deployed on the server.
- If you want to perform some action at the time of deploying the web application such as creating database connection, creating all the tables of the project etc, you need to implement ServletContextListener interface and provide the implementation of its methods.

`Web application > ServletContextEvent > ServletContextListener`

### HttpSessionEvent and HttpSessionListener

- The HttpSessionEvent is notified when session object is changed. The corresponding Listener interface for this event is HttpSessionListener.
- We can perform some operations at this event such as counting total and current logged-in users, maintaing a log of user details such as login time, logout time etc.

`session object > HttpSessionEvent > HttpSessionListener`