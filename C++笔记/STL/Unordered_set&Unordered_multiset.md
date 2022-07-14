<!--
 * @Author: your name
 * @Date: 2022-03-31 11:41:26
 * @LastEditTime: 2022-03-31 14:18:13
 * @LastEditors: Please set LastEditors
 * @Description: 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
 * @FilePath: /workspace/MyBlog/C++/STL/Unordered_set&Unordered_multiset.md
-->
# Unordered_set&Unordered_multiset

## 说明

### 引用库

```cpp
#include <unordered_set>

namespace std{
    template<typename T, typename Hash = hash<T>, typename EqPred = equal_to<T>, typename Allocator = allocator<T>>
    class unorder_set;
    template<typename T,typename Hash = hash<T>, typname EqPred = equal_to<T>,typename Allocator = allocator<T>>
    class unordered_multiset;
}
```

### 本质

``unordered_set``, ``unordered_multiset``是C++11中以hash table(哈希表)为基础的，内部元素没有明确的顺序的容器。

### 特点

## 创建与初始化

## 基础属性

## 赋值

## 元素访问

## 迭代器相关

## 增删

## 异常

## 特殊