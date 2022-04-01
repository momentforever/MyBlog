<!--
 * @Author: your name
 * @Date: 2021-09-17 09:07:59
 * @LastEditTime: 2021-09-17 14:51:38
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /workspace/Studynote/网络开发笔记·/第十五章-套接字和标准IO.md
-->
# 套接字和标准I/O

## 标准I/O的优点

+ 标准I/O函数具有良好的移植性
+ 标准I/O函数可以利用缓冲提高性能

## 标准I/O的缺点

+ 不容易进行双向通信
+ 有时可能频繁调用fflush函数
> fflush: 切换读写工作状态
+ 需要以FILE结构体指针的形式返回文件描述符

## 使用标准I/O函数

```cpp
#include <stdio.h> 
FILE *fdopen(int filedes,const char *mode);
/*
成功是返回转换的FILE结构体指针，失败时返回NULL
fildes: 需要转换的文件描述符
mode: 将要创建的FILE结构体指针的模式信息
*/
```

```cpp
#include <stdio.h>

int fileno(FILE *stream);
/*
成功是返回文件描述符，失败时返回 -1
*/
```

