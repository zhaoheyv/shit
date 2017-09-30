---
layout: post
title : POSIX线程相关基础知识
author: 赵何宇
---

> 主要参考[IBM DeveloperWork](https://www.ibm.com/developerworks/cn/)，非常不错的一个站。

###### 基本概念

1. 线程共享相同的内存空间，不同线程可以读取内存中的同一个变量，由此程序中的所有线程都可以读或写声明过的全局变量。
2. fork()函数创建多个进程带来的进程间通信(IPC)问题，因为进程有各自独立的内存空间。
3. 由于所有的线程都驻留在同一内存空间，POSIX 线程无需进行开销大而复杂的长距离调用。只要利用简单的同步机制，程序中所有的线程都可以读取和修改已有的数据结构。而无需将数据经由文件描述符转储或挤入紧窄的共享内存空间。
4. 可以与同进程中的其他线程共享数据，但拥有自己的栈空间，拥有独立的执行序列。

###### POSIX创建线程函数
```
int  pthread_create(pthread_t  *  thread, pthread_attr_t * attr, void * (*start_routine)(void *), void * arg)
```
###### 线程取消函数
```
int pthread_cancel(pthread_t thread);
/*发送终止信号给thread线程，如果成功则返回0，否则为非0值。发送成功并不意味着thread会终止。*/
int pthread_setcancelstate(int state, int *oldstate); 
/*设置本线程对Cancel信号的反应，state有两种值：PTHREAD_CANCEL_ENABLE（缺省）和PTHREAD_CANCEL_DISABLE，分别表示收到信号后设为CANCLED状态和忽略CANCEL信号继续运行；old_state如果不为NULL则存入原来的Cancel状态以便恢复。*/
int pthread_setcanceltype(int type, int *oldtype); 
/*设置本线程取消动作的执行时机，type由两种取值：PTHREAD_CANCEL_DEFFERED和PTHREAD_CANCEL_ASYCHRONOUS，仅当Cancel状态为Enable时有效，分别表示收到信号后继续运行至下一个取消点再退出和立即执行取消动作（退出）；oldtype如果不为NULL则存入运来的取消动作类型值。*/
void pthread_testcancel(void); 
/*检查本线程是否处于Canceld状态，如果是，则进行取消动作，否则直接返回。*/
```