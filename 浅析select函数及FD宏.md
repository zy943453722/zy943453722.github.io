---
title: 浅析select函数及FD宏
date: 2017-09-19 23:13:33
tags: [linux,网络编程，c]
categories: 技术
comments: true
---
# select函数
**这个函数允许进程指示内核等待多个事件中的任一个发生，并仅在一个或者多个事件发生或经过某指定时间后才唤醒进程。**

### 函数原型：###
```
#include<sys/select.h>
#include<sys/time.h>

int select(int maxfdp1, fd_set *readset, fd_set *writeset, fd_set *exceptset, const struct timeval *timeout);
     返回：准备好的描述字正数目，0表示超时，-1表示出错
```
### 函数参数解析：
1. 参数maxfdp1指定被测试的描述字数目，即经过FD_SET之后，所关心的描述符的个数，（因为从0开始，因此一般这个值是测试描述符集下标最大值+1）并通知内核。
2. 中间三个参数readset，writeset，exceptset指定我们要让内核测试读、写、异常条件所需的描述字。也就是说，对于readset，**我们关心是否可以从这些文件中读取数据了**，对于writeset，**我们关心是否可以向这些文件中写入数据了**，对于exceptset，**我们关心这些文件是否发生异常**。
3. 参数timeout，它告诉内核等待一组指定的描述字中的任意一个准备好可花多长时间，结构timeval指定了秒数和微秒数成员：
```
struct timeval
{
long tv-sec;//秒
long tv-usec;//微秒
}
```
***有三种可能：***
 
 1) 、永远等待下去：仅在有一个描述符准备好I/O时才返回，要设置timeout为NULL。
 
 2) 、等待固定时间：在有一个描述符准备好I/O时返回，但不超过timeout参数指定的秒数和微秒数。
 
 3) 、根本不等待：检查描述字后立即返回，这称为轮询(polling),参数timeout要指向某一个timeval结构，且其中的秒数和微秒数要置0。
 
 **前两种是阻塞的，最后一种是不阻塞的**
 
 ## fdset结构体解析:
 ```
 #include<typesizes.h>
 #define __FD_SETSIZE 1024
 
 #include<sys/select.h>
 #define FD_SETSIZE __FD_SETSIZE
 typedef long int __fd_mask;//64位系统上大小为8
 #undef __NFDBITS
 #define __NFDBITS (8*(int) sizeof(__fd_mask))  // 8*8 = 64
 
 typedef struct
 {
 #ifdef __USE_XOPEN
     __fd_mask fds_bits[__FD_SETSIZE / __NFDBITS];//数组长度为1024/64 = 16
  #define __FDS_BITS(set) ((set)->fds_bits)
  #else
     __fd_mask __fds_bits[____FD_SETSIZE / __NFDBITS];
  #define __FDS_BITS(set) ((set)->fds_bits)
  #endif
  }fd_set
 ```
 **因此fd_set结构体中仅包含一个整型数组，该数组的每个元素的每一位都会标记一个文件描述符。因此16个long int元素就是 16*8*8 = 1024位 ，最多容纳1024个文件描述符！！**
 
 # select中FD相关的4个宏
 1.  void FD_ZERO(fd_set *fdset);//使fd_set结构体中的整型数组清零
 2.  void  FD_SET(int fd,fd_set *fdset); //设置感兴趣的文件描述符
 3.  void FD_CLR（int fd,fd_set *fdset);//关闭感兴趣的文件描述符
 4.  int FD_ISSET(int fd, fd_set *fdset); //用于判断某个文件描述符是否就绪，且在感兴趣的描述符集合中
  
# 对于几个宏和select的理解
* fd_set是创建文件描述符集合
* FD_SET是设置感兴趣的文件描述符放入描述符集合中，将对应位置为1，但是此文件描述符可能准备好了，也可能没有准备好，可以说是一类文件描述符。
* select是监听这几个文件描述符看哪几个就绪了，就返回个数，没就绪的再次置回0，就绪的保留1。
* FD_ISSET是真正查看哪个指定的文件描述符是否就绪，就绪就为1，未就绪就为0。

***example：***
   
   为说明方便，取fd_set长度为1字节，fd_set中的每一bit可以对应一个文件描述符fd。则1字节长的fd_set最大可以对应8个fd。
　　
  （1）执行fd_set set; FD_ZERO(&set);则set用位表示是0000,0000。
　　
 
 （2）若fd＝5,执行FD_SET(fd,&set);后set变为0001,0000(第5位置为1)
　
 
 （3）若再加入fd＝2，fd=1,则set变为0001,0011

（4）执行select(6,&set,0,0,0)阻塞等待

（5）若fd=1,fd=2上都发生可读事件，则select返回，此时set变为0000,0011。注意：没有事件发生的fd=5被清空。

（6）此时FD_ISSET(1,&set)返回1，而FD_ISSET(5,&set)返回0
