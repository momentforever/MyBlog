<!--
 * @Author: your name
 * @Date: 2021-11-02 11:10:43
 * @LastEditTime: 2021-11-15 13:54:05
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /workspace/Blog/C++/STL/Array.md
-->

# Array

## 说明

### 引用库

```cpp
#include <array>

namespace std {
    template<typename T,size_t N>
    class array;
}
```

### 本质

array实际上是对c/c++语言中原生数组进行的封装。

### 特点

内存分配在栈(stack)，绝对不会重新分配，随机访问元素。

## 初始化与创建

```cpp

    // 未初始化
    std::array<int, 100> a;
    // 初始化为0
    std::array<int, 100> b = {};
    // 按照数组初始化
    std::array<int,5> c = {1,2,3,4,5};
    // 其余值初始化为0
    std::array<int,5> d = {1}; 
```

## 基础属性
```cpp
    // 判断是否为空
    a.empty(); // never be true if size > 0
    // 返回长度
    a.size(); 
    // 返回最大容量 size() == max_size()
    a.max_size();

    // 重载运算符
    // operator == != < > <= >=
    // 首先比较长度，再逐一比较每个元素的大小
    a == b;
    a != b;
    a > b;
    a < b;
    a <= b;
    a >= b;
```

## 元素访问
```cpp
    a[1];
    //at会检查下标是否合法
    a.at(1);
    // 返回第一个元素，不是迭代器
    a.front();
    // 返回最后一个元素，不是迭代器
    a.back();
```
### Note

```cpp
    if(c.size() > 3) {
        c[3] = 10;
    }
```


## 赋值

```cpp
    // 直接赋值
    auto aa = a;
    // 将数组 a 和 aa 进行替换
    aa.swap(a);
    swap(aa,a);
```

## 迭代器相关

```cpp
    //声明迭代器
    std::array<int, 100>::iterator iter1;
    std::array<int, 100>::reverse_iterator iter2;
    std::array<int, 100>::const_iterator iter3;
    std::array<int, 100>::const_reverse_iterator iter4;
    //迭代器相关
    a.begin();
    a.end();
    //const
    a.cbegin();
    a.cbegin();
    //尾部->头部
    a.rbegin();
    a.rend();
    //const 尾部->头部
    a.crbegin();
    a.crend();
```

## 增删

同数组。

## 异常

```cpp
    // 异常处理
    // c.at(pos);
    // copy move swap
```

## 特殊

```cpp
    //和C的接口互用
    std::array<char, 100> carr;
    strcpy(&carr[0],"hello world\n"); // more better use carr.data();
    printf("%s",&carr[0]); // more better use carr.data()
    printf("%s",carr.begin()); // error

    // 特殊的地方
    auto info = std::get<1>(carr);
    carr.fill(0);
```