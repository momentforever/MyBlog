<!--
 * @Author: your name
 * @Date: 2021-09-08 10:13:55
 * @LastEditTime: 2021-09-08 10:24:29
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /workspace/Studynote/网络开发笔记·/UDP.md
-->
# UDP

## UDP的数据I/O函数

```cpp
ssize_t sendto(int sock,void *buff,size_t nbytes,int flags,
                struct sockaddr *to,socklen_t);
/*
成功时返回传输的字节数，失败时返回 -1
sock:用于传输数据的UDP套接字
buff:保存待传输数据的缓冲地址值
nbytes:待传输的数据长度，以字节为单位
flags:可选项参数，若没有则传递 0
to:存在目标地址的sockaddr结构体变量的地址值
addrlen:传递给参数to的地址值结构体变量长度
*/
```

```cpp
ssize_t recvfrom(int sock,void *buff,size_t nbytes,int flags,
                    struct sockaddr *from,socklen_t *addrlen);
/*
成功时返回传输的字节数，失败时返回 -1
sock:用于接收数据的UDP套接字文件描述符
buff:保存接收数据的缓冲地址值
nbytes:可接收的最大字节数，古无法超过参数buff所指的缓冲大小
flags:可选参数，若没有则传递 0
from:存有发送端地址信息的sockaddr结构体变量的地址值
addrlen:保存参数from的结构体变量长度的变量地址值
*/
```