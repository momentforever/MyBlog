<!--
 * @Author: your name
 * @Date: 2021-09-14 09:54:52
 * @LastEditTime: 2021-09-24 09:31:20
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /workspace/Studynote/网络开发笔记·/第十二章-IO复用.md
-->
# I/O复用

## select函数

```cpp
#inlcude <sys/select.h>
#include <sys/time.h>

int select(int maxfd,fd_set *readset, fd_set *writeset,
            fd_set *exceptset, const struct timeval *timeout);

/*
成功时返回大于 0 的值，失败时返回 -1
maxfd: 监控对象文件描述符数量
readset: 将所有关注[是否存在待读取数据]的文件描述符到 fd_set 型变量，并传递其地址值
writeset: 将所有关注[是否可传输无阻塞数据]的文件描述符到 fd_set 型变量，并传递其地址值
exceptset: 将所有关注[是否发生异常]的文件描述符注册到 fd_set 型变量，并传递其地址值
timeout: 调用 select 函数后，为防止陷入无限阻塞的状态，传递超时(time-out)信息
return: 发生错误时返回 -1， 超时时返回 0 。因发生关注的时间返回时，返回大于 0 的值，该值时发生事件的文件描述符。
*/
```

## timeval结构体
```cpp
struct timeval{
    long tv_sec;
    long tv_usec;
}
```

## fd_set操作

+ FD_ZERO(fd_set *fdset): 将fd_set变量所指的位全部初始化为0
+ FD_SET(int fd,fd_set *fdset): 在参数fdset指向的变量中注册文件描述符fd的信息
+ FD_SLR(int fd,fd_set *fdset): 从参数fdset指向的变量中清除文件描述符fd的信息
+ FD_ISSET(int fd,fd_set *fdset): 若参数fdset指向的变量中包含文件描述符fd的信息，则返回 True
