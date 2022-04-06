<!--
 * @Author: your name
 * @Date: 2021-09-18 16:31:46
 * @LastEditTime: 2021-12-13 09:50:48
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /workspace/Studynote/网络开发笔记·/第十六章-关于IO流分离的内容.md
-->
# IO流分离的内容

## IO流分离

1. 通过调用fork函数复制出文件描述符，以区分输入和输出中使用的文件描述符。
2. 通过2次fopen函数调用，创建读模式FILE指针（FILE结构体指针）和写模式FILE指针。

## 分离流的好处

1. fork
+ 通过分开输入过程（代码）和输出过程降低实现难度
+ 与输入无关的输出操作可以提高速度

2. fopen
+ 为了将FILE指针按读模式和写模式加以区分
+ 可以通过区分读写模式降低实现难度
+ 通过区分I/O缓冲提高观缓冲性能

```cpp
shutdown(sock,SHUT_WR);
```

## dup和dup2

```cpp
#include <unistd.h>

int dup(int fildes);
int dup2(int fildes, int fildes2);
/*
成功时返回复制的文件描述符，失败时返回 -1
fildes: 需要复制的文件描述符
fildes2: 明确指定的文件描述符的正整数 
*/
```