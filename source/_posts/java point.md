---
title: java io
date: 2019-07-10 14:25:30
tags: learn
categories: java
---

# base

## 类型

- int变量自增 效率问题 

  ```
  i++;和i += 1;形成的指令数较少，且不需要操作数栈。相对而言，i = i+1;需要至少2个操作数栈深度，并且形成的指令数较多。故，i++;和i+= 1;的效率一样，并且二者的效率都优于i = i+1;
  ```

## 泛型

**T，E，K，V，？是这样约定的：**

- ？表示不确定的 java 类型
- T (type) 表示具体的一个java类型
- K V (key value) 分别代表java键值中的Key Value
- E (element) 代表Element

**？和 T 的区别**：

![img](https://mmbiz.qpic.cn/mmbiz/knmrNHnmCLFRVA73vYfkqVGfia90X5nyEsksfM8Vz6btFZCRd3PCSJC1XkZhaJ0ufxcoohxAdr8X1tykU9CZPUQ/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

？和 T 都表示不确定的类型，区别在于我们可以对 T 进行操作，但是对 ？不行，比如如下这种 ：

```
// 可以
T t = operate();

// 不可以
？car = operate();
```

简单总结下：

T 是一个 确定的 类型，通常用于泛型类和泛型方法的定义，？是一个 不确定 的类型，通常用于泛型方法的调用代码和形参，不能用于定义类和泛型方法。

**PECS（Producer Extends Consumer Super）**原则：作为生产者提供数据（往外读取）时，适合用上界通配符（extends）；作为消费者消费数据（往里写入）时，适合用下界通配符（super）。

## 时间

在 JDK8 以后，建议使用 DateTimeFormatter 代替 SimpleDateFormat ，因为 SimpleDateFormat 是线程不安全的，而 DateTimeFormatter 是线程安全的。当然，也可以采用第三方提供的线程安全日期格式化函数，比如 apache 的 DateFormatUtils 工具类。

## dynamic compile

静态编译：编译时就把所有用到的Java代码全都编译成字节码，是一次性编译。

动态编译：在Java程序运行时才把需要的Java代码的编译成字节码，是按需编译。

从JDK1.6开始，引入了Java代码重写过的编译器接口，使得我们可以在运行时编译Java源代码，然后再通过类加载器将编译好的类加载进JVM,这种在运行时编译代码的操作就叫做动态编译。

---

[【Java动态编译】动态编译的应用_牛客博客](https://blog.nowcoder.net/n/d2a7554ea2ec4e4b978cf4a74c3c41b2)

[Java动态性(1) - 动态编译(DynamicCompile)](https://segmentfault.com/a/1190000016842546)



## IO流

```
流包括字符流和字节流，流从概念上来说是一个连续的数据流。当程序需要读数据的时候就需要使用输入流读取数据，当需要往外写数据的时候就需要输出流
```

- 字节流：字节流主要用来处理字节或二进制对象`InputStream、OutputStream，`

  > 在使用字节流的时候，InputStream和OutputStream都是抽象类，我们实例化的都是他们的子类，每一个子类都有自己的作用范围

  ![](https://i.imgur.com/s39Xj3r.png)

- 字符流：字符流用来处理字符文本或字符串`Reader、Writer`

  > 在使用字符流的时候也是，Reader和Writer都是抽象类，我们实例化的都是他们的子类，每一个子类都有自己的作用范围

  ![](https://i.imgur.com/RMD0koG.png)

- 字节流转换为字符流

  > 使用`InputStreamReader、OutputStreamWriter`可以将输入字节流转化为输入字符流
  >
  > ```java
  > Reader reader  =  new InputStreamReader(inputStream);
  > Writer writer = new OutputStreamWriter(outputStream)
  > ```

输入字节流：**InputStream**

```java
public static void main(String[] args) throws Exception{
    File file = new File("D:/a.txt");
    InputStream inputStream = new FileInputStream(file);
    byte[] bytes = new byte[(int) file.length()];
    inputStream.read(bytes);
    System.out.println(new String(bytes));
    inputStream.close();
}
```

输入字符流：**Reader**

```java
public static void main(String[] args) throws Exception{
    File file = new File("D:/a.txt");
    Reader reader = new FileReader(file);
    char[] bytes = new char[(int) file.length()];
    reader.read(bytes);
    System.out.println(new String(bytes));
    reader.close();
}
```

输出字节流：**OutputStream**

```java
public static void main(String[] args) throws Exception{
    String var = "hai this is a test";
    File file = new File("D:/b.txt");
    OutputStream outputStream = new FileOutputStream(file);
    outputStream.write(var.getBytes());
    outputStream.close();
}
```

输出字符流：**Writer**

```java
public static void main(String[] args) throws Exception{
    String var = "hai this is a test";
    File file = new File("D:/b.txt");
    Writer writer = new FileWriter(file);
    writer.write(var);
    writer.close();
}
```

**BufferedInputStream**

> 在使用InputStream的时候，都是一个字节一个字节的读或写，而BufferedInputStream为输入字节流提供了缓冲区，读数据的时候会一次读取一块数据放到缓冲区里，当缓冲区里的数据被读完之后，输入流会再次填充数据缓冲区，直到输入流被读完，有了缓冲区就能够提高很多io速度

```java
/**
 * inputStream 输入流
 * 1024 内部缓冲区大小为1024byte
 */
BufferedInputStream bufferedInputStream = new BufferedInputStream(inputStream,1024);
```

**BufferedOutputStream**

> BufferedOutputStream可以为输出字节流提供缓冲区，作用与BufferedInputStream类似，使用方式将输出流包装到BufferedOutputStream中

```java
/**
 * outputStream 输出流
 * 1024 内部缓冲区大小为1024byte
 */
BufferedOutputStream bufferedOutputStream = new BufferedOutputStream(outputStream,1024);
```

**BufferedReader**

> 为输入字符流提供缓冲区

```java
BufferedReader bufferedReader = new BufferedReader(reader,1024);
```

**BufferedWriter**

> 为输出字符流提供缓冲区

```java
BufferedWriter bufferedWriter = new BufferedWriter(writer,1024);
```



## io模型

### bio

> 同步阻塞IO模型
>
> JDK 1.4版本以前

![](https://chenmingyu.top/nio/bio%E7%BA%BF%E7%A8%8B.png)

在这种模式中通常用一个线程去接受请求，然后用一个线程池去处理请求，用这种方式并发管理多个Socket客户端连接

![](https://chenmingyu.top/nio/BIO%E7%BA%BF%E7%A8%8B+%E7%BA%BF%E7%A8%8B%E6%B1%A0.png)

### nio

> 同步非阻塞IO模型
>
> JDK 1.4版本以后

成熟的框架，比如Netty

### aio

> 异步非阻塞IO模型
>
> JDK1.7升级了Nio类库，成为Nio2.0，最主要的是提供了异步文件的IO操作，以及事件驱动IO，AIO的异步套接字通道是真正的异步非阻塞IO



![](https://chenmingyu.top/nio/%E5%90%84IO%E6%A8%A1%E5%9E%8B%E5%AF%B9%E6%AF%94.png)

---

参考：

[一文看懂java io系统](<https://chenmingyu.top/nio/>)

# Web Application

### HTML and HTTP

- HTML:HyperText Markup Language.
- HTTP:HyperText Transfer Protocol
  - HTTP is the communication protocol between server and client. HTTP runs on top of TCP/IP communication protocol.
  - Port numbers 0 to 1023 are reserved ports for well known services, for example 80 for HTTP, 443 for HTTPS, 21 for FTP etc.
- Java Servlet and JSPs are server side technologies to extend the capability of web servers by providing support for dynamic response and data persistence.

### Web Container

- When web container gets the request and if it’s for servlet then container creates two Objects HTTPServletRequest and HTTPServletResponse. Then it finds the correct servlet based on the URL and creates a thread for the request. Then it invokes the servlet service() method and based on the HTTP method service() method invokes doGet() or doPost() methods. Servlet methods generate the dynamic page and write it to response. Once servlet thread is complete, container converts the response to HTTP response and send it back to client.
- Some of the important work done by web container are:
  - Communication Support – Container provides easy way of communication between web server and the servlets and JSPs. Because of container, we don’t need to build a server socket to listen for any request from web server, parse the request and generate response. All these important and complex tasks are done by container and all we need to focus is on our business logic for our applications.
    - Lifecycle and Resource Management – Container takes care of managing the life cycle of servlet. Container takes care of loading the servlets into memory, initializing servlets, invoking servlet methods and destroying them. Container also provides utility like JNDI for resource pooling and management.
    - Multithreading Support – Container creates new thread for every request to the servlet and when it’s processed the thread dies. So servlets are not initialized for each request and saves time and memory.
    - JSP Support – JSPs doesn’t look like normal java classes and web container provides support for JSP. Every JSP in the application is compiled by container and converted to Servlet and then container manages them like other servlets.
    - Miscellaneous Task – Web container manages the resource pool, does memory optimizations, run garbage collector, provides security configurations, support for multiple applications, hot deployment and several other tasks behind the scene that makes our life easier.
- Java Web Applications are packaged as Web Archive (WAR) and it has a defined structure. 
  ![](https://cdn.journaldev.com/wp-content/uploads/2013/08/WAR-directory-structure.png)

## Servlet

- Servlet API Hierarchy
  ![](https://cdn.journaldev.com/wp-content/uploads/2013/08/Servlet-Hierarchy.png)

## Session Management

### What is a Session?

- HTTP protocol and Web Servers are stateless, what it means is that for web server every request is a new request to process and they can’t identify if it’s coming from client that has been sending request previously.
- Session is a conversional state between client and server and it can consists of multiple request and response between client and server. Since HTTP and Web Server both are stateless, the only way to maintain a session is when some unique information about the session (session id) is passed between server and client in every request and response.

### JSESSIONID Cookie

- When we use HttpServletRequest getSession() method and it creates a new request, it creates the new HttpSession object and also add a Cookie to the response object with name JSESSIONID and value as session id. This cookie is used to identify the HttpSession object in further requests from client. If the cookies are disabled at client side and we are using URL rewriting then this method uses the jsessionid value from the request URL to find the corresponding session. JSESSIONID cookie is used for session tracking, so we should not use it for our application purposes to avoid any session related issues.
- When a JSP resource is used, container automatically creates a session for it, so we can’t check if session is null to make sure if user has come through login page, so we are using session attribute to validate request.
- As we saw in last section that we can manage a session with HttpSession but if we disable the cookies in browser, it won’t work because server will not receive the JSESSIONID cookie from client. 

#### Cookie Basics 

- a cookie is a small piece of data stored on the client-side which servers use when communicating with clients.They’re used to identify a client when sending a subsequent request. They can also be used for passing some data from one servlet to another.

# design pattern

## Creational Design Patterns

> These design patterns provide a way to create objects while hiding the creation logic, rather than instantiating objects directly using new operator. This gives program more flexibility in deciding which objects need to be created for a given use case.

### Singleton

> In Factory pattern, we create object without exposing the creation logic to the client and refer to newly created object using a common interface.

- different approaches to implement:

  - Private constructor to restrict instantiation of the class from other classes.
  - Private static variable of the same class that is the only instance of the class.
  - Public static method that returns the instance of the class, this is the global access point for outer world to get the instance of the singleton class.

- design concerns with the implementation

  - Eager initialization
  - Static block initialization
  - Lazy Initialization
  - Thread Safe Singleton
  - Bill Pugh Singleton Implementation
  - Using Reflection to destroy Singleton Pattern
  - Enum Singleton
  - Serialization and Singleton

  ```java
  //SingleObject.java
  public class SingleObject {
  
     //create an object of SingleObject
     private static SingleObject instance = new SingleObject();
  
     //make the constructor private so that this class cannot be
     //instantiated
     private SingleObject(){}
  
     //Get the only object available
     public static SingleObject getInstance(){
        return instance;
     }
  
     public void showMessage(){
        System.out.println("Hello World!");
     }
  }
  
  //SingletonPatternDemo.java
  public class SingletonPatternDemo {
     public static void main(String[] args) {
  
        //illegal construct
        //Compile Time Error: The constructor SingleObject() is not visible
        //SingleObject object = new SingleObject();
  
        //Get the only object available
        SingleObject object = SingleObject.getInstance();
  
        //show the message
        object.showMessage();
     }
  }
  ```

  

### Factory

- Factory design pattern provides approach to code for interface rather than implementation.
- Factory pattern removes the instantiation of actual implementation classes from client code. Factory pattern makes our code more robust, less coupled and easy to extend. For example, we can easily change PC class implementation because client program is unaware of this.
- Factory pattern provides abstraction between implementation and client classes through inheritance.

### Abstract Factory

- Abstract Factory design pattern provides approach to code for interface rather than implementation.
- Abstract Factory pattern is “factory of factories” and can be easily extended to accommodate more products, for example we can add another sub-class Laptop and a factory LaptopFactory.
- Abstract Factory pattern is robust and avoid conditional logic of Factory pattern.

### Builder

- Builder pattern solves the issue with large number of optional parameters and inconsistent state by providing a way to build the object step-by-step and provide a method that will actually return the final Object.

### Prototype

- Prototype design pattern is used when the Object creation is a costly affair and requires a lot of time and resources and you have a similar object already existing.Prototype pattern provides a mechanism to copy the original object to a new object and then modify it according to our needs. Prototype design pattern uses java cloning to copy the object.

## Structural Design Patterns

> These design patterns concern class and object composition. Concept of inheritance is used to compose interfaces and define ways to compose objects to obtain new functionalities.

### Adapter

- Adapter design pattern is one of the structural design pattern and its used so that two unrelated interfaces can work together. The object that joins these unrelated interface is called an Adapter.

### Composite

- Composite design pattern is used when we have to represent a part-whole hierarchy.

### Proxy

- Proxy design pattern common uses are to control access or to provide a wrapper implementation for better performance.

### Flyweight

- Use sharing to support large numbers of fine-grained objects efficiently

### Facade

- Provide a unified interface to a set of interfaces in a subsystem. Facade Pattern defines a higher-level interface that makes the subsystem easier to use.

### Bridge

- Decouple an abstraction from its implementation so that the two can vary independently.

### Decorator

- Decorator design pattern is helpful in providing runtime modification abilities and hence more flexible. Its easy to maintain and extend when the number of choices are more.
- The disadvantage of decorator design pattern is that it uses a lot of similar kind of objects (decorators).

## Behavioral Design Patterns

> These design patterns are specifically concerned with communication between objects.

### Template Method

- Template method should consists of certain steps whose order is fixed and for some of the methods, implementation differs from base class to subclass. Template method should be final.
- Most of the times, subclasses calls methods from super class but in template pattern, superclass template method calls methods from subclasses, this is known as Hollywood Principle – “don’t call us, we’ll call you.”.
- Methods in base class with default implementation are referred as Hooks and they are intended to be overridden by subclasses, if you want some of the methods to be not overridden, you can make them final, for example in our case we can make buildFoundation() method final because if we don’t want subclasses to override it.

### Media

- Allows loose coupling by encapsulating the way disparate sets of objects interact and communicate with each other. Allows for the actions of each object set to vary independently of one another.