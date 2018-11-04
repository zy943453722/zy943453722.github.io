---
title: I/O复用之poll和epoll
date: 2017-11-12 21:59:25
tags: [linux,网络编程,c]
categories: IPC进程通信
comments: true
---
## poll函数
***poll函数的定义：***
```
#include<poll.h>
int poll(struct pollfd* fds,nfds_t nfds,int timeout)
typedef unsigned long int nfds_t;
struct pollfd{
   int fd;
   short events;//注册的事件
   short revents;//实际发生的事件，有内核填充
};
timeout为0时，poll调用后立即返回，不阻塞
       为-1时，poll调用将永远阻塞，直至某个事件发生。
```
***poll函数实现监视的过程：***

*  调用poll函数实现对感兴趣的文件描述符的某个事件的监听。
* 而后读取返回值，若返回值大于0，则轮询创建好的pollfd结构体数组，看每一个注册的事件是否发生感兴趣的事件。

## epoll函数

### epoll函数的定义：
**<br>epoll函数不同于poll和select，它是由一组函数组成的。**
***<br>1. epoll创建函数：***
```
#include<sys/epoll.h>
int epoll_create(int size)//参数指定内核事件表的大小,返回一个指向内核事件表的fd：epollfd
```
***<br>2. epoll执行注册函数：***
```
/*第一个参数为内核事件表的文件描述符，第二个参数为指定的操作类型,
第三个为要操作的文件描述符，第4个为注册的事件*/
//返回0为成功，-1为失败
int epoll_ctl(int epollfd,int op,int fd,struct epoll_event *event)
```
**对于op操作类型，大体有3种：**
 * EPOLL_CTL_ADD:往内核事件表注册事件结构体
 * EPOLL_CTL_MOD:修改fd上的注册事件
 * EPOLL_CTL_DEL:删除fd上的注册事件
**<br>对于epoll_event结构体：**
```
typedef union epoll_data{
   void *ptr;
   int fd;//注册事件的文件描述符
   uint32_t u32;
   uint64_t u64;
}epoll_data_t;
  struct epoll_event{
   __uint32_t events;//epoll事件
   epoll_data_t data;//用户的数据
};//一般是将事件和epoll_data_t用户数据绑定
```
***<br>3. epoll等待就绪函数：***
```
/*第一个参数指定内核事件表，第二个参数指定从内核事件表拷贝的就绪事件数组，
第三个参数指定最多监听的事件个数，最后一个是设置超时时间同epoll*/
//该函数成功时返回就绪事件个数，与文件描述符无关，失败返回-1
int epoll_wait(int epollfd,struct epoll_event* events, int max,int timeout)
```
***<br>4.epoll的事件类型***
* EPOLLIN   ：数据可读（包括一般和优先数据）
* EPOLLOUT  ：数据可写（包括一般和优先数据）
* EPOLLERR ：数据错误
* EPOLLET ：epoll的边沿触发模式事件
* EPOLLONESHOT ：该文件描述符上的可读、可写、错误事件最多触发其中一个且只触发一次
  **<br>最后两个是epoll特有的，前三个去掉'E'即为poll的事件类型**
***<br>5.epoll的模式：***
<br>epoll中有两种模式，一种是LT模式，一种是ET模式。
* LT模式：
  <br><font color=red size = 3>所谓LT模式，就是指电平触发，这是epoll默认的工作方式，要想改变可以通过上面的EPOLLET事件。这种模式下的epoll相当于一个效率较高的poll。<br><br>**这个模式下epoll_wait检测到有事件就绪后，应用程序可以不立即处理，那么下次调用epoll_wait仍会通告该事件，直至被处理。意味着每次epoll_wait()返回后，事件处理后，如果之后还有数据，会不断触发，也就是说，一个套接字上一次完整的数据，epoll_wait()可能会返回多次，直到没有数据为止。**这个模式下文件描述符可阻塞可不阻塞。<br>该模式下只要在可读/可写 状态下epoll_wait都能检测到，因此可以不一次处理完。</font>
* ET模式  :
<br><font color = blue size = 3>所谓ET模式，就是指边沿触发，当某个文件描述符注册EPOLLET事件时，eopll将使用ET模式操作该文件描述符。<br>有数据过来后，epoll_wait()会返回一次，**一段时间内，**该套接字就算有数据源源不断地过来，epoll_wait()也不会返回了。（即只会读出其中第一次的内容，再来数据是读不到的，但若是缓冲区不够的话，也会读到结束的）这里注意，是一段时间，不代表这个套接字上有数据就只触发一次。时间过长，还是会返回多次的。**<br>该模式与LT的最大区别就是当epoll_wait检测到事件就绪时，必须立即处理，后序再遇到epoll_wait不会在通告该事件，这样大大降低了同一个epoll事件被触发的次数。**该模式下文件描述符必须使用非阻塞。**这种模式下发送多次否则，会因为缓冲区的不足造成发送或接收不到所有的数据。直到errno为EAGAIN时才算读(写)取完毕无数据可读(写）**<br>另外，该模式只有当某文件描述符由不可读/不可写 状态变为 可读/可写 状态时才能触发</font>
<font size = 5 color = green><br>因此，ET模式是高速工作模式，LT是缺省工作模式。</font>
***<br>6.网络事件EAGIN***
<BR>在一个非阻塞的socket上调用read/write函数, 返回EAGAIN或者EWOULDBLOCK。若遇到能read时，读缓冲区没有数据，或者write时，写缓冲区满了。这种情况下，如果是阻塞socket，read/write就要阻塞掉。而如果是非阻塞socket，read/write立即返回-1， 同时errno设置为EAGAIN。
<br>因此，对于non-blocking的socket，正确的读写操作为:
<br>读：忽略掉errno = EAGAIN的错误，下次可继续读
<br>写：忽略掉errno = EAGAIN的错误，下次可继续写
<br>因此ET模式使用非阻塞IO模型下需要用EAGAIN这个标志来判断接受的数据是否读完。