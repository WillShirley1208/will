---
title: java backend
date: 2016-03-1 09:00:30
tags: java
categories: java backend
---

# filter in servlet

> A filter is an object that is invoked at the preprocessing and postprocessing of a request.

- It is mainly used to perform filtering tasks such as conversion, logging, compression, encryption and decryption, input validation etc.
- The servlet filter is pluggable, i.e. its entry is defined in the web.xml file, if we remove the entry of filter from the web.xml file, filter will be removed automatically and we don't need to change the servlet.

![](https://www.javatpoint.com/images/filter.JPG)

### Usage of Filter

- recording all incoming requests
- logs the IP addresses of the computers from which the requests originate
- conversion
- data compression
- encryption and decryption
- input validation etc.

### Advantage of Fliter

- Filter is pluggable.
- One filter don't have dependency onto another resource.
- Less Maintenance

### Filter API

- Filter

  > For creating any filter, you must implement the Filter interface. Filter interface provides the life cycle methods for a filter.

- FilterChain

  > The object of FilterChain is responsible to invoke the next filter or resource in the chain.This object is passed in the doFilter method of Filter interface.

  ```java
  public class MyFilter implements Filter{  
  public void init(FilterConfig arg0) throws ServletException {}  
  
      public void doFilter(ServletRequest req, ServletResponse resp,  
        FilterChain chain) throws IOException, ServletException {  
  
            PrintWriter out=resp.getWriter();  
            out.print("filter is invoked before");  
  
            chain.doFilter(req, resp);//sends request to next resource  
  
            out.print("filter is invoked after");  
        }  
        public void destroy() {}  
  }      
  ```

```
- FilterConfig
```



# Event and Listen

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

# other

### generate code based proto file

`protoc --proto_path=扫描目录 --java_out=代码生成目录 proto文件目录`

> protoc --proto_path=src --java_out=build/gen src/foo.proto

### Spring provides three way of scheduling:

- @Scheduled
- Via Quartz
- Via JDK Timer

---

### one word

- \x对应的是UTF-8编码的数据