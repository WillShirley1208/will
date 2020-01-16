---
title: word record
date: 2017-05-30 15:43:50
tags: work
categories: tips
---

**关于代码编译的事情**

- 问题
  - 由于一些配置项写在统一的配置文档中，代码从配置文件中读取确定的配置项；此时操作的文本路径是要追加上根目录的路径，然而开发环境和生成环境是不一样的。项目在打包的过程中，就确定了文本路径，这样导致两边环境的配置文档读取结果不一致。

- 解决办法
  - 把文件路径精确写入
  - 在生成环境下编译代码

---

**关于读取数据量大的excel文件**

- 问题

  - 项目部署到tomcat中，每次读取大于1M的文件时，会停滞在`org.apache.poi.ss.usermodel.Workbook workbook = new XSSFWorkbook(is);`,直到tomcat出现报错信息`java.lang.OutOfMemoryError: Java heap space `,即java虚拟机堆溢出的错误。
- 现象描述
  - 手动设置Xms,Xmx的大小，在/bin/catalina.bat（windows环境）配置中，添加`JAVA_OPTS="-server -Xms10G -Xmx20G"`,不知道什么原因，重启服务，仍旧报一样的错。（当我在自己电脑上运行程序，操作同样大小的文档时，也没有报错，何况自己电脑才8G内存，远不及服务器，再则就是自身电脑tomcat的配置也是默认的）

  - 内存总体分为年轻代(young),老年代(old),永久代(permanent),如图

    ![7OCHl.jpg](https://s1.ax2x.com/2018/05/31/7OCHl.jpg)