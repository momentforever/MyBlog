<!--
 * @Author: your name
 * @Date: 2021-09-23 15:02:55
 * @LastEditTime: 2021-09-24 11:00:23
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /workspace/Studynote/网络开发笔记·/第十七章-优于select的epoll.md
-->
# 优于select的epoll

## select的I/O复用技术慢的原因

+ 调用``select``函数后常见的针对所有文件描述符的循环语句
+ 每次调用``select``函数时都需要向该函数传递监视对象信息

``select``函数的缺点可以通过如下方式弥补：
> 仅向操作系统传递一次监视对象，监视范围或内容发生变化时只通知发送变化的事项

这样就无需每次调用``select``函数时都向操作系统传递监视对象，但是前提操作系统支持这种处理方式。Linux的支持方式时epoll，Windows的支持方式时IOCP。

## 实现epoll时必要的函数和结构体

+ ``epoll_create``: 创建保存epoll文件描述符的空间
+ ``epoll_ctl``: 向空间注册并注销文件描述符
+ ``epoll_wait``: 与select函数类似，等待文件描述符发生变化
  
```cpp
struct epoll_event{
    __uint32_t events;
    epool_data_t data;
}

typedef union epoll_data {
    void *ptr;
    int fd;
    __unint32_t 32;
    __unint32_t 64;
} epoll_data_t;
```

### epoll_create

```cpp
#include <sys/epoll.h>
#int epoll_create(int size);
/*
成功是返回 epoll的文件描述符，失败时返回 -1
size: epoll实例的大小
*/
```
epoll_create函数时创建的文件描述符保存空间称为``epoll例程``,但有些情况下名称不同，需要稍加注意。
通过参数size传递的值决定epoll例程的大小，但该值只是向操作系统提出建议。换言之，size并不用来决定epoll的大小，而仅供操作系统参考。

> Linux 2.6.8之后的内核将完全传如epoll_create函数的size参数，因此内核会根据情况调整epoll例程大小。

### epoll_ctl

```cpp
#include <sys/epoll.h>
int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);
/*
成功时返回 0 ，失败时返回 -1
epfd: 用于注册监视对象的epoll例程的文件描述符
op: 用于指定监视对象的添加、删除或更改等操作
fd: 需要注册的监听对象文件描述符
event: 监视对象的事件类型
*/
```

```cpp
epoll_ctl(A,EPOLL_CTL_ADD,B,C);
// 第二个参数 EPOLL_CTL_ADD 意味着 添加
// epoll例程 A 中注册文件描述符 B ，主要目的是为了监视参数 C 中的事件

epoll_ctl(A,EPOLL_CTL_DEL,B,NULL);
// 第二个参数 EPOLL_CTL_DEL 意味着 删除
// 从epoll例程 A 中删除文件描述符 B
```

+ EPOLL_CTL_ADD: 将文件描述符注册到epoll例程
+ EPOLL_CTL_DEL: 从epoll例程中删除文件描述符
+ EPOLL_CTL_MOD: 更改注册的文件描述符的关注事件发生情况
  
```cpp
struct epoll_event event;
event.events = EPOLLIN; //发生需要读取数据的情况时
event.data.fd = sockfd;
epoll_ctl(epfd,EPOLL_CTL_ADD,sockfd,&event);
```

+ EPOLLIN: 需要读取数据的情况
+ EPOLLOUT: 输入缓冲为空，可以立刻发送数据的情况
+ EPOLLPRI: 收到OOB数据的情况
+ EPOOLRDHUP: 断开连接或半连接的情况，这在边缘触发方式下非常有用
+ EPOLLERR: 发生错误的情况
+ EPOLLET: 以边缘触发的方式得到消息通知
+ EPOLLONESHOT: 发生一次事件后，相应文件描述符不在收到事件通知。因此需要向epoll_ctl函数的第二个参数传递EPOLL_CTL_MOD，再次设置事件。

可通过位运算同时传递多个上述参数。

### epoll_wait

```cpp
#include <sys/epoll.h>
int epoll_wait(int epfd,struct epoll_event *events,int maxevents,int timeout);
/*
成功时返回发生事件的文件描述符，失败时返回 -1
epfd: 表示事件发生监视范围的epoll例程的文件描述符
events: 保存发生事件的文件描述符集合的结构体地址值
maxevents: 第二个参数中可以保存的最大事件数
timeout: 以1/1000秒为单位的等待时间，传递 -1 时，一直等待直到发生事件
*/
```

```cpp
int event_cnt;
struct epoll_event *ep_events;

ep_events = malloc(sizeof(struct epoll_event)*EPOLL_SIZE);//EPOLL_SIZE是宏常量

event_cnt = epoll_wait(epfd,ep_events,EPOLL_SIZE,-1);
```

## epoll的流程

1. ``epoll_create``创建一个保存epoll文件描述符的空间，可以没有参数
2. 动态分配内存，给将要监视的``epoll_wait``
3. 利用``epoll_ctl``控制 添加、删除，监听事件
4. 利用``epoll_wait``来获取改变的文件描述符，来执行程序
   
## select和epoll的区别：

+ 每次调用select函数都会向操作系统传递监视对象，并且每次都需O（n）的复杂度来轮询所有流。
+ epoll仅向操作系统传递一次监听对象，并且监听内容发送变化时只通知发生变化的事项（O（1））

## 条件触发和边缘触发

条件触发特性：
> 条件触发方式中，只要输入缓冲有数据就会一直通知该事件

边缘触发特性：
> 边缘触发中，输入缓冲收到数据时仅注册一次事件。即使输入缓冲中海留有数据，也不会再进行注册


### 边缘触发的服务器端必知的两点

+ 通过errno变量验证错误原因
+ 为了完成非阻塞(Non-blocking) I/O，更改了套接字特性

Linux套接字相关的函数一般通过-1通知发生了错误。虽然知道发生了错误，但仅凭这些内容无法得知产生错误的原因。因此，为了在发生错误的时候提供额外的信息，Linux声明了如下全局变量

```cpp
#include <error.h>
int errno;
```

```cpp
#include <fcntl.h>
int fcntl(int fields, int cmd, ...);
/*
成功时返回cmd相关值，失败时返回 -1
filedes: 属性更改目标的文件描述符
cmd: 表示函数调用目的
*/
```

```cpp
int flag = fcntl(df,F_GETEL,0);
fcntl(fd,F_SETDL | O_NONBLOCK);
```
通过第一条语句，获取之前设置的属性信息，通过第二条语句在此基础只是添加非阻塞O_NONBLOCK标志。调用read/write函数后，无论是否存在数据，都会形成非阻塞文件（套接字）。fcntl函数的适用范围很广。