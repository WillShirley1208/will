---
title: websocket tracking
date: 2016-01-18 19:20:30
tags: learn
categories: webservice
---

https://www.jianshu.com/p/49d7997ad3b7

https://blog.csdn.net/qq_33546747/article/details/53304097

------

## WebService是一种跨编程语言和跨操作系统平台的远程调用技术。

多个角度来理解WebService
​	从表面上看，WebService就是一个应用程序向外界暴露出一个能通过Web进行调用的API，也就是说能用编程的方法通过Web来调用这个应用程序。
​	我们把调用这个WebService的应用程序叫做客户端，而把提供这个WebService的应用程序叫做服务端
​	

```
从深层次看，WebService是建立可互操作的分布式应用程序的新平台，是一个平台，是一套标准。
它定义了应用程序如何在Web上实现互操作性，你可以用任何你喜欢的语言，在任何你喜欢的平台上写Web service ，只要我们可以通过Web service标准对这些服务进行查询和访问。
```

------

构成WebService平台的三大技术：
​	XML+XSD,SOAP和WSDL
​	
WebService采用HTTP协议传输数据，采用XML格式封装数据（即XML中说明调用远程服务对象的哪个方法，传递的参数是什么，以及服务对象的返回结果是什么）
WebService用XSD来作为其数据类型系统
HTTP消息头和XML内容格式就是SOAP协议。SOAP提供了标准的RPC方法来调用Web Service。

SOAP协议 = HTTP协议 + XML数据格式

比喻：HTTP就是普通公路，XML就是中间的绿色隔离带和两边的防护栏，SOAP就是普通公路经过加隔离带和防护栏改造过的高速公路。

WSDL(Web Services Description Language)是基于XML的语言，用于描述Web Service及其函数、参数和返回值。

WSDL文件保存在Web服务器上，通过一个url地址就可以访问到它。客户端要调用一个WebService服务之前，要知道该服务的WSDL文件的地址。
WebService服务提供商可以通过两种方式来暴露它的WSDL文件地址：1.注册到UDDI服务器，以便被人查找；2.直接告诉给客户端调用者。

======================================================================================
https://www.cnblogs.com/snake-hand/archive/2013/06/09/3129915.html

------

Axis和CXF，这两个产品都是Apache孵化器下面的Web Service开源开发工具

CXF支持 WS-Addressing，WS-Policy， WS-RM， WS-Security和WS-I Basic Profile
Axis2不支持WS-Policy

CXF可以很好支持Spring
Axis2不能很好支持Spring

 CXF只支持JAXB和Aegis。
 AXIS2支持更广泛的数据并对，如XMLBeans，JiBX，JaxMe和JaxBRI和它自定义的数据绑定ADB。注意JaxME和JaxBRI都还是试验性的。AXIS2支持更广泛的数据并对，如XMLBeans，JiBX，JaxMe和JaxBRI和它自定义的数据绑定ADB。注意JaxME和JaxBRI都还是试验性的。

------

 如何抉择：
1、如果应用程序需要多语言的支持，Axis2 应当是首选了；
2、如果应用程序是遵循Spring 哲学路线的话，Apache CXF 是一种更好的选择，特别对嵌入式的Web Services 来说；
3、如果应用程序没有新的特性需要的话，就仍是用原来项目所用的框架，比如Axis1，XFire，Celtrix或 BEA等等厂家自己的 WebServices 实现，就别劳民伤财了







