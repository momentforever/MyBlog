<!--
 * @Author: your name
 * @Date: 2021-09-14 14:12:33
 * @LastEditTime: 2021-09-15 13:56:53
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /workspace/Studynote/网络开发笔记·/第十三章-多种IO函数.md
-->
# 多种I/O函数

## send 函数
```cpp
#include <sys/socket.h>
ssize_t send(int sock, const void *buf,size_t nbytes, int flags);
/*
成功时返回发送的字节数，失败时返回 -1
sockfd: 表示与数据传输对象的连接套接字和文件描述符
buf: 保存带传输数据和缓冲地址值
nbytes: 待传输字节数
flags: 传输数据时指定的可选项信息
*/
```

## recv函数

```cpp
#include <sys/socket.h>
ssize_t recv(int sockfd,void *buf, size_t nbytes, int flags);
/*
成功时返回接收的字节数（收到EOF返回0），失败时返回 -1
sockfd: 表示数据接受对象的连接的套接字文件描述符
buf: 保存接收数据的缓冲地址值
nbytes: 可接收的最大字节数
flags: 接收数据时指定的可选项参数
*/
```

## flags可选项

该选项可以用位或(bit OR)运算符(|运算符)同时传递多个信息。

|可选项(Option)|含义|send|recv|
|---|---|---|---|
|MSG_OOB|用于传输带外数据(Out-of-band data)|√|√|
|MSG_PEEK|验证输入缓冲中是否存在接收的数据|×|√|
|MSG_DONTROUTE|数据传输过程中不参照本地路由(Routing)表，在本地(Local) 网络中寻找目的地|√|×|
|MSG_DONTWAIT|调用I/O函数时不阻塞，用于使用非阻塞(Non_blocking)I/O|√|√|
|MSG_WAITALL|防止函数返回，直到接收到全部请求的字节数|×|√|

### MSG_OOB

带外数据，很少使用


## readv & writev 函数


```cpp
#include <sys/uio.h>
ssize_t writev(int filedes, const struct iovec *iov, int iovcnt);
/*
成功时返回发送的字节数，失败时返回 -1
filedes: 表示数据传输对象的套接字文件描述符。但该函数并不仅限于套接字，因此，可以像 read 一样向其传递文件或者标准输出描述符。
iov:  向第二个参数传递数组长度
*/
```

```cpp
#include <sys/uio.h>
ssize_t readv(int filedes, const struct iovc *iov,int iovcnt);
/*
成功时返回接收的字符数，失败时返回 -1
filedes: 表示数据传输对象的套接字文件描述符。但该函数并不仅限于套接字，因此，可以像 read 一样向其传递文件或者标准输出描述符。
iov:  向第二个参数传递数组长度
*/
```


### iovec结构体

```cpp
struct iovec{
    void *iov_base; // 缓冲地址
    size_t iov_len; // 缓冲大小  
};
```

