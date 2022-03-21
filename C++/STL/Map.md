<!--
 * @Author: your name
 * @Date: 2021-11-18 16:25:13
 * @LastEditTime: 2022-01-17 13:55:36
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /workspace/Blog/C++/STL/Map.md
-->

# Map

## 说明

### 引用库

```cpp
#include <map>

namespace std {
    template<typename Key,typename T,typename Compare = less<Key>,typename Allocator = allocator<pair<const Key,T>>>
    class map;
}
```

### 本质

map 是C++98中引入的二叉树数据结构。

### 特点

+ 自动将元素排序
+ 插入和删除查找O(log(n))
+ Key元素必须支持严格的弱排序

1. x < y == true, y < x == false
2. x < y == true, y < x == true,x < z == true
3. x < x === false
4. a== b, b == c, c == a

+ 不能改变Key元素的值

## 创建与初始化

```cpp
    std::map<int,std::string> a;
    Grostd::map<int,std::string>up b = a;
    std::map<int,std::string> c(a);
    std::map<int,std::string> d(c.begin(),c.end());
    std::map<int, std::string> g({ {1,"a"},{2,"test"},{3,"test"}});
```

## 基础属性

```cpp
    // 判断是否为空
    d.empty();
    // 返回长度
    d.size();
    // 返回可扩容的最大容量
    d.max_size();

    //operator == != < > <= >=

    //special
    auto keytcomp = c.key_comp();
    auto valuecomp = c.value_comp();
```

## 赋值

```cpp
    b = g;

    // 将二叉树 a 和 b 进行替换
    b.swap(a);
    std::swap(a,b);
```

## 元素访问

```cpp
    // 不能随机访问
    // b[0];
    // b.at(0);
    
    // 返回第一个元素，不是迭代器
    b.front();
    // 返回最后一个元素，不是迭代器
    b.back();
```

## 迭代器相关
```cpp
    std::map<int,std::string>::iterator iter1;
    
    //元素访问，不能随机访问
    //b[0];
    //b.at(0);
    b.front();
    b.back();

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
    auto iterBegin = a.begin();
```

## 增删

## 异常

## 特殊