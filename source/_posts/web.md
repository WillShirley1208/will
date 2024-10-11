---
title: web
date: 2016-12-18 18:01:30
tags: snippet
categories: web
---

# websocket

https://blog.csdn.net/LOVELONG8808/article/details/52235132

https://blog.csdn.net/w1992wishes/article/details/79583543
********************************************************************************************************
WebSocket API是下一代客户端-服务器的异步通信方法。
WebSocket API最伟大之处在于服务器和客户端可以在给定的时间范围内的任意时刻，相互推送信息。
WebSocket并不限于以Ajax(或XHR)方式通信，因为Ajax技术需要客户端发起请求，而WebSocket服务器和客户端可以彼此相互推送信息；
​	XHR受到域的限制，而WebSocket允许跨域通信。
​	Ajax技术很聪明的一点是没有设计要使用的方式。WebSocket为指定目标创建，用于双向推送消息。

-----------------------------------------------------------------------------------------------------------------
activemq是消息中间件，主要实现是队列。用于处理消息。 
websocket是 html5的一种传议实现前台向后台，后台向前台发出请求。
-----------------------------------------------------------------------------------------------------------------
websocket是HTML5协议，实时，全双工通信，长连接。WebSocket的服务端可以将数据推送到客户端，
​	如实时将证券信息反馈到客户端，实时天气数据，比http请求响应模式更灵活，代替了以往一些需要轮训的业务。

STOMP协议的前身是TTMP协议（一个简单的基于文本的协议），专为消息中间件设计。

这两个协议的介绍来看设计的目的不同。目前一些消息队列都是基于STOMP协议的比如ActiveMQ，RabbitMQ，消息队列一般用于一些需要异步处理的服务器任务或者一些通知类的任务。

websocket更多的使用场景是需要服务端主动通知客户端的实时通讯业务。

# webservice

https://www.jianshu.com/p/49d7997ad3b7

https://blog.csdn.net/qq_33546747/article/details/53304097

**WebService是一种跨编程语言和跨操作系统平台的远程调用技术**

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





