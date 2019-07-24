---
title: java io
date: 2019-07-10 14:25:30
tags: learn
categories: java
---

## 流

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