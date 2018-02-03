---
layout: post
title : Linux 系统调用
author: 赵何宇
---

> 主要参考资料，[IBM博客](https://www.ibm.com/developerworks/cn/linux/kernel/syscall/part1/)

##### 概念：
1.Linux内核中一组实现各种系统功能的子程序，成为系统调用。

2.系统调用由操作系统核心提供，运行于核心态。

3.普通的函数调用由函数库或用户自己提供，运行于用户态。

4.随Linux核心还提供了一些C语言函数库，这些库对系统调用进行了一些包装和扩展，因为这些库函数与系统调用的关系非常紧密，所以习惯上把这些函数也称为系统调用。

##### 如何工作的：
一般的，进程是不能访问内核的。它不能访问内核所占内存空间也不能调用内核函数。CPU硬件决定了这些（这就是为什么它被称作"保护模式"）。系统调用是这些规则的一个例外。其原理是进程先用适当的值填充寄存器，然后调用一个特殊的指令，这个指令会跳到一个事先定义的内核中的一个位置（当然，这个位置是用户进程可读但是不可写的）。在Intel CPU中，这个由中断0x80实现。硬件知道一旦你跳到这个位置，你就不是在限制模式下运行的用户，而是作为操作系统的内核--所以你就可以为所欲为。

进程可以跳转到的内核位置叫做sysem_call。这个过程检查系统调用号，这个号码告诉内核进程请求哪种服务。然后，它查看系统调用表(sys_call_table)找到所调用的内核函数入口地址。接着，就调用函数，等返回后，做一些系统检查，最后返回到进程（或到其他进程，如果这个进程时间用尽）。如果你希望读这段代码，它在<内核源码目录>/kernel/entry.S，Entry(system_call)的下一行。

##### 例子：
~~~
#include<linux/unistd.h> /*定义宏_syscall1*/
#include<time.h>     /*定义类型time_t*/
_syscall1(time_t,time,time_t *,tloc)    /*宏，展开后得到time()函数的原型*/
main()
{
        time_t the_time;
        the_time=time((time_t *)0); /*调用time系统调用*/
        printf("The time is %ld\n",the_time);
}
系统调用time返回从格林尼治时间1970年1月1日0:00开始到现在的秒数。
这是最标准的系统调用的形式，宏_syscall1()展开来得到一个函数原型，稍后我会作详细解释。但事实上，如果把程序改成下面的样子，程序也可以运行得同样的结果。
#include<time.h>
main()
{
        time_t the_time;
        the_time=time((time_t *)0); /*调用time系统调用*/
        printf("The time is %ld\n",the_time);
}
~~~
