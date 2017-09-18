---
layout: post
title : Socket 编程
author: 赵何宇
---

> Linux下的socket简单总结和代码实现。参考了[IBM developWorks](https://www.ibm.com/developerworks/cn/education/linux/l-sock/l-sock.html)&[CSDN博客](http://www.cnblogs.com/luxiaoxun/archive/2012/10/16/2725760.html)

使用TCP/IP协议的应用程序通常采用应用编程接口：UNIX  BSD的套接字（socket）和UNIX System V的TLI（已经被淘汰），来实现网络进程之间的通信。

几个字节顺序转换函数： 
```
htons()--"Host to Network Short" ; htonl()--"Host to Network Long" 
ntohs()--"Network to Host Short" ; ntohl()--"Network to Host Long" 
// h表示"host" ，n表示"network"，s 表示"short"，l表示 "long"。
```

#### socket 类型

常见的socket有3种类型如下。 
1. 流式socket（SOCK_STREAM ） 
流式套接字提供可靠的、面向连接的通信流；它使用TCP 协议，从而保证了数据传输的正确性和顺序性。 
2. 数据报socket（SOCK_DGRAM ） 
数据报套接字定义了一种无连接的服 ，数据通过相互独立的报文进行传输，是无序的，并且不保证是可靠、无差错的。它使用数据报协议UDP。 
3. 原始socket（SOCK_RAW）
原始套接字允许对底层协议如IP或ICMP进行直接访问，功能强大但使用较为不便，主要用于一些协议的开发。

#### socket()函数



#### bind()函数

#### listen()、connect()函数

#### accept()函数

#### read()、write()函数等

#### close()函数