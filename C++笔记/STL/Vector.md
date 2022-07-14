<!--
 * @Author: your name
 * @Date: 2021-10-29 16:34:08
 * @LastEditTime: 2022-03-31 09:39:13
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /workspace/Blog/C++/STL/Vector.md
-->
# Vector

## 说明

### 引用库

```cpp
#include <vector>

namespace std {
    template<typename T,typename Allocator = allocator<T>>
    class vector;
}
```

### 本质

vector 是C++98中引入的动态数组(dynamic array)。

连续存储结构，每个元素在内存上是连续的；支持 高效的随机访问和在尾端插入/删除操作，但其他位置的插入/删除操作效率低下； 相当于一个数组，但是与array的区别为；内存空间的扩展。

### 特点

随机访问内元素，末端添加删除元素效率高。前端和中间删除和添加元素效率低，存在当前容器大小和容量的关系。

PS: 每次扩容(超过``capacity()``)会重新声明一端连续的内存空间，并重新赋值到新的内存空间中。所以尽可能避免频繁扩容动态数组。 

## 创建与初始化

```cpp
    // 直接声明一个动态数组
    std::vector<float> a;
    // 将动态数组 a 复制动态数组 b (值复制)
    std::vector<float> b = a;
    // 将动态数组 a 复制动态数组 b (值复制)
    std::vector<float> c(a);
    // 创建一个长度为10的动态数组，并将所有元素初始化为0.0f(默认)
    std::vector<float> d(10);
    // 创建一个长度为10的动态数组，并将所有元素初始化为1.0f
    std::vector<float> e(10,1.0f);
    // 传入迭代器初始化动态数组
    std::vector<float> f(e.begin(),e.end());
    // 以填入方式初始化动态数组
    std::vector<float> g({1.0f,2.0f,3.0f});
    // c++11
    // 赋值的方式直接初始化数组
    std::vector<float> h = {1,0f,3.0f,4.0f}; 
```

## 基础属性

```cpp
    // 判断是否为空
    a.empty();
    // 返回长度
    a.size();
    // 返回可扩容的最大容量
    a.max_size();
    // 在不声明新的内存空间下，能扩容的大小
    a.capacity();
    // 使得内存空间至少能容纳100个元素
    a.reserve(100);
    // c++11
    // 使内存空间恰好符合容量
    a.shrink_to_fit(); 

    // b.size() == 10
    b.resize(10);
    // b.size() == 100
    // 所有初始值为 1.0f
    b.resize(100,1.0f);
    // 清空数组
    b.clear(); //notic

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

## 赋值

```cpp
    // 直接赋值
    b = a;
    // 将动态数组 b 的长度设置为 3 ，并将值设置为 1.0f
    b.assign(3,1.0f); // {1.0f, 1.0f, 1.0f}
    // 将动态数组 b 初始化，将[a.begin(),a.end()]赋值给 b
    b.assign(a.begin(),a.end()); // b = a
    // 将双向链表 b 初始化，并将{1.0f,2.0f,3.0f}赋值给 b 
    b.assign({1.0f,2.0f,3.0f}); 
    // 将动态数组 a 和 b 进行替换
    b.swap(a);
    std::swap(a,b);
```

## 元素访问

```cpp
    // 直接访问
    b[0];
    // 直接访问
    b.at(0); //std::out_of_range
    // 返回第一个元素，不是迭代器
    b.front();
    // 返回最后一个元素，不是迭代器
    b.back(); 
```

### Note
在使用``back()``或``front()``应先判断动态数组是否为空。
```cpp
    if(b.empty()){
        b.front(); //undefined
        b.back(); // undefined
    }
```

## 迭代器相关

```cpp
    //声明迭代器
    std::vector<float>::iterator iter1;
    std::vector<float>::reverse_iterator iter2;
    std::vector<float>::const_iterator iter3;
    std::vector<float>::const_reverse_iterator iter4;
    //迭代器相关
    a.begin();
    a.end();
    auto iterBegin = a.begin();
    for(auto it = a.begin();it < a.end();it++){
        //...
    }
    //const
    a.cbegin();
    a.cbegin();
    //尾部->头部
    a.rbegin();
    a.rend();
    auto riterBegin = a.rbegin();
    // < 或 !=
    for(auto rit = a.rbegin();rit < a.rend();rit++){
        //...
    }
    //const 尾部->头部
    a.crbegin();
    a.crend();
```

## 增删

```cpp
    // 尾部插入一个元素
    b.push_back(10.0f);
    // 尾部弹出一个元素，ps: 没有返回值
    // 当动态数组为空时，直接报错
    a.pop_back();
    // 例子
    // 非线程安全
    if(!a.empty())a.pop_back());

    // 删除指定元素
    // b.erase(b.begin());
    // 返回下一个位置的迭代器
    auto iterAfter = b.erase(b.begin());
    
    // 删除两迭代器中间的值
    b.erase(b.begin(),b.end());
    
    // pos,var
    auto iter = b.insert(b.end(),100.0f);
    // pos,num,var
    iter = b.insert(b.end(),10,-10.0f);
    // pos,start,end
    b.insert(b.end(),h.begin(),h.end());
    // 插入元素
    b.emplace(b.end(),10.0f); //C++11 && move copy&&
    b.emplace_back(10.0f);
```

## 异常

```cpp
    // 异常处理
    // push_back
    // 元素 move/copy 没有异常的话， insert emplace emplace_back push_back
    // pop_back
    // 元素 move/copy 没有异常的话， erase
    // swap clear
```

## 特殊

```cpp
    //std::vector<bool> never use it
```