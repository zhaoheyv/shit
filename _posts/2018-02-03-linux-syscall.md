---
layout: post
title : Linux 系统调用
author: 赵何宇
---

> 主要参考资料，[IBM博客](https://www.ibm.com/developerworks/cn/linux/kernel/syscall/part1/)

###### 概念：
1.Linux内核中一组实现各种系统功能的子程序，成为系统调用。

2.系统调用由操作系统核心提供，运行于核心态。

3.普通的函数调用由函数库或用户自己提供，运行于用户态。

4.随Linux核心还提供了一些C语言函数库，这些库对系统调用进行了一些包装和扩展，因为这些库函数与系统调用的关系非常紧密，所以习惯上把这些函数也称为系统调用。

###### 如何工作的：
一般的，进程是不能访问内核的。它不能访问内核所占内存空间也不能调用内核函数。CPU硬件决定了这些（这就是为什么它被称作"保护模式"）。系统调用是这些规则的一个例外。其原理是进程先用适当的值填充寄存器，然后调用一个特殊的指令，这个指令会跳到一个事先定义的内核中的一个位置（当然，这个位置是用户进程可读但是不可写的）。在Intel CPU中，这个由中断0x80实现。硬件知道一旦你跳到这个位置，你就不是在限制模式下运行的用户，而是作为操作系统的内核--所以你就可以为所欲为。

进程可以跳转到的内核位置叫做sysem_call。这个过程检查系统调用号，这个号码告诉内核进程请求哪种服务。然后，它查看系统调用表(sys_call_table)找到所调用的内核函数入口地址。接着，就调用函数，等返回后，做一些系统检查，最后返回到进程（或到其他进程，如果这个进程时间用尽）。如果你希望读这段代码，它在<内核源码目录>/kernel/entry.S，Entry(system_call)的下一行。

###### 例子：
~~~
#include<linux/unistd.h> /*定义宏_syscall1，在unistd.h里面有7个宏定义*/
#include<time.h>     /*定义类型time_t*/
_syscall1(time_t,time,time_t *,tloc)    
/*宏，展开后得到time()函数的原型*/
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

###### errno：
为防止和正常的返回值混淆，系统调用并不直接返回错误码，而是将错误码放入一个名为errno的全局变量中。如果一个系统调用失败，你可以读出errno的值来确定问题所在。

errno不同数值所代表的错误消息定义在errno.h中，你也可以通过命令"man 3 errno"来察看它们。

需要注意的是，errno的值只在函数发生错误时设置，如果函数不发生错误，errno的值就无定义，并不会被置为0。另外，在处理errno前最好先把它的值存入另一个变量，因为在错误处理过程中，即使像printf()这样的函数出错时也会改变errno的值。

###### fork:
~~~
pid_t pid;
pid = fork();
if (pid == 0){
  printf("child %d\n", getpid());
}else{
  printf("parent %d\n", getpid());
}
~~~
在父进程中，fork返回新创建子进程的进程ID；

在子进程中，fork返回0；

如果出现错误，fork返回一个负值:两个原因导致，（1）当前的进程数已经达到了系统规定的上限，这时errno的值被设置为EAGAIN。（2）系统内存不足，这时errno的值被设置为ENOMEM。

###### exit:
无论在程序中的什么位置，只要执行到exit系统调用，进程就会停止剩下的所有操作，清除包括PCB在内的各种数据结构，并终止本进程的运行。
~~~
#include <stdlib.h>

main(){
  printf("bulabulabulabula\n", );
  exit(0);
  printf("bulabulabulabula\n", );// 不会执行
}
~~~
exit系统调用带有一个整数类型的参数status，我们可以利用这个参数传递进程结束时的状态，比如说，该进程是正常结束的，还是出现某种意外而结束的
![exit和_exit区别](https://www.ibm.com/developerworks/cn/linux/kernel/syscall/part2/1_1.JPG)

###### wait
wait的函数原型是：
~~~
#include <sys/types.h> /* 提供类型pid_t的定义 */
#include <sys/wait.h>
pid_t wait(int *status)
~~~
进程一旦调用了wait，就立即阻塞自己，由wait自动分析是否当前进程的某个子进程已经退出，如果让它找到了这样一个已经变成僵尸的子进程，wait就会收集这个子进程的信息，并把它彻底销毁后返回；如果没有找到这样一个子进程，wait就会一直阻塞在这里，直到有一个出现为止。

参数status用来保存被收集进程退出时的一些状态，它是一个指向int类型的指针。但如果我们对这个子进程是如何死掉的毫不在意，只想把这个僵尸进程消灭掉，（事实上绝大多数情况下，我们都会这样想），我们就可以设定这个参数为NULL，就象下面这样：
~~~
pid = wait(NULL);
~~~
如果成功，wait会返回被收集的子进程的进程ID，如果调用进程没有子进程，调用就会失败，此时wait返回-1，同时errno被置为ECHILD。
###### 进程同步
比如说父进程等待子进程结束。
~~~
#include <sys/types.h>
#include <sys/wait.h>
main()
{
    pid_t pc, pr;
    int status;

    pc=fork();

    if(pc<0)
        printf("Error occured on forking.\n");
    else if(pc==0){
        /* 子进程的工作 */
        exit(0);
    }else{
        /* 父进程的工作 */
        pr=wait(&status); //阻塞在这里
        /* 利用子进程的结果 */
    }
}
~~~
###### waidpid
waitpid多出了两个可由用户控制的参数pid和options。
waitpid系统调用在Linux函数库中的原型是：
~~~
#include <sys/types.h> /* 提供类型pid_t的定义 */
#include <sys/wait.h>
pid_t waitpid(pid_t pid,int *status,int options)
~~~
###### exec
exec指的是一组函数，一共有6个，分别是：
~~~
#include <unistd.h>
int execl(const char *path, const char *arg, ...);
int execlp(const char *file, const char *arg, ...);
int execle(const char *path, const char *arg, ..., char *const envp[]);
int execv(const char *path, char *const argv[]);
int execvp(const char *file, char *const argv[]);
int execve(const char *path, char *const argv[], char *const envp[]);
~~~
exec函数族的作用是根据指定的文件名找到可执行文件，并用它来取代调用进程的内容，换句话说，就是在调用进程内部执行一个可执行文件。这里的可执行文件既可以是二进制文件，也可以是任何Linux下可执行的脚本文件。
exec函数族的函数执行成功后不会返回，只有调用失败了，它们才会返回一个-1，从原程序的调用点接着往下执行。
~~~
int execve(const char *path, char *const argv[], char *const envp[])
int main(int argc, char *argv[], char *envp[])
~~~
DEMO:
~~~
/* exec.c */
#include <unistd.h>
main()
{
    char *envp[]={"PATH=/tmp",
            "USER=lei",
            "STATUS=testing",
            NULL};
    char *argv_execv[]={"echo", "excuted by execv", NULL};
    char *argv_execvp[]={"echo", "executed by execvp", NULL};
    char *argv_execve[]={"env", NULL};
    if(fork()==0)
        if(execl("/bin/echo", "echo", "executed by execl", NULL)<0)
            perror("Err on execl");
    if(fork()==0)
        if(execlp("echo", "echo", "executed by execlp", NULL)<0)
            perror("Err on execlp");
    if(fork()==0)
        if(execle("/usr/bin/env", "env", NULL, envp)<0)
            perror("Err on execle");
    if(fork()==0)
        if(execv("/bin/echo", argv_execv)<0)
            perror("Err on execv");
    if(fork()==0)
        if(execvp("echo", argv_execvp)<0)
            perror("Err on execvp");
    if(fork()==0)
        if(execve("/usr/bin/env", argv_execve, envp)<0)
            perror("Err on execve");
}
~~~
