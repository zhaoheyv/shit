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

```
int socket(int family, int type, int protocol);

unsigned int sockSrv = socket(AF_INET, SOCK_STREAM, 0);
```

family指定协议族；type参数指定socket的类型：SOCK_STREAM、SOCK_DGRAM、SOCK_RAW；protocol通常赋值"0"。
socket()调用返回一个整型socket描述符，你可以在后面的调用使用它。 一旦通过socket调用返回一个socket描述符，你应该将该socket与你本机上的一个端口相关联（往往当你在设计服务器端程序时需要。随后你就可以在该端口监听服务请求;而客户端一般无须调用该函数）。

#### bind()函数
```
int bind(int sockfd, struct sockaddr *my_addr, int addrlen); 

ret = bind(sockfd, (SOCKADDR*)&sockaddr, sizeof(SOCKADDR)); 

//defines the sockaddr struct as follows:

struct sockaddr {
    u_short sa_family;              /* address family */
    char    sa_data[14];            /* up to 14 bytes of direct address */
};

struct sockaddr_in {
	short int sin_family; // Address family, AF_INET
	unsigned short int sin_port; // Port number
	struct in_addr sin_addr; // Internet address
	unsigned char sin_zero[8]; // Same size as struct sockaddr
};

```
sockfd 为绑定的socket描述符，函数返回值0，-1(操作失败)，和通常的调用方式中包含的类型转换。
sockaddr_in 也是16Byte的结构，指针位置一样所以就可以相互转换。

#### listen() 函数

#### connect()函数

#### accept()函数

#### read()、write()函数等

#### close()函数