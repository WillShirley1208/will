---
title: socket snippet
date: 2018-05-9 08:01:30
tags: snippet
categories: Socket
---

## TCP and UDP

>  - TCP stands for Transmission Control Protocol and UDP stands for User Datagram Protocol, and both are used extensively to build Internet applications.
>  - The protocol which is the core of internet, HTTP is based on TCP.

### why 

>  why Java developer should understand these two protocols in detail is that Java is extensively used to write multi-threaded, concurrent and scalable servers.

### where

>  While UDP is more suitable for applications that need fast, efficient transmission, such as games. UDP's stateless nature is also useful for servers that answer small queries from huge numbers of clients. In practice, TCP is used in finance domain e.g. [FIX protocol](http://javarevisited.blogspot.sg/2011/04/fix-protocol-tutorial-for-beginners.html) is a TCP based protocol, UDP is used heavily in gaming and entertainment sites.

### how

-   TCP is connection oriented, reliable, slow, provides guaranteed delivery and preserves the order of messages

-   UDP is connectionless, unreliable, no ordering guarantee, but a fast protocol. 

-   TCP overhead is also much higher than UDP, as it transmits more metadata per packet than UDP.  that header size of Transmission control protocol is 20 bytes, compared to 8 bytes header of User Datagram protocol.

-   Use TCP, if you can't afford to lose any message, while UDP is better for high-speed data transmission, where loss of a single packet is acceptable e.g. video streaming or online multiplayer games.




