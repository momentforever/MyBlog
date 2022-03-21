<!--
 * @Author: your name
 * @Date: 2021-11-17 09:56:26
 * @LastEditTime: 2021-11-17 10:48:24
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /workspace/Blog/C++/STL/Forward_list.md
-->
# Forward_list

## 说明

### 引用库

```cpp
#include <forward_list>
namespace std {
    template<typename T,typename Allocator = allocator<T>>
    class forward_list;
}
```

### 本质

forward_list 是C++11中引入的单项串列(single linked list)

### 特点

不支持随机访问元素，访问头部元素速度快

“``forward_list``和自己手写的c-style singly linked list相比，没有任何时间和空间上的额外开销，任何性质如果和这个目标抵触，我们放弃该特征。”

任何位置插入和删除元素都很快，常量时间内完成。

插入和删除都不会造成迭代器失效。

对异常支持的好，出现异常对于forward_list而言，要么成功，要么什么影响都没没有。

## 创建与初始化
```cpp
    std::forward_list<float> a;
    std::forward_list<float> b = a;
    std::forward_list<float> c(a);
    std::forward_list<float> d(10);
    std::forward_list<float> e(10,1.0f);
    std::forward_list<float> f(e.begin().e.end());
    std::forward_list<float> g({1.0f,2.0f,3.0f});
    std::forward_list<float> h = {1.0f,3.0f,4.0f};
```

## 基础属性
```cpp
    // 判断是否为空
    d.empty();
    // 返回长度
    d.size();
    // 返回可扩容的最大容量
    d.max_size();

    // d.size() == 10
    d.resize(10);
    // d.size() == 100
    // 所有初始值为 1.0f
    d.resize(100,1.0f);
    // 清空数组
    d.clear(); 
    
```
### Note
```cpp
    //和vector不同，list不提供以下的函数
    //d.capacity();
    //d.reserve(100);
    //d.shrink_to_fit();
    //operator == != < > <= >=
```

## 赋值
```cpp
    // 直接赋值
    b = g;
    // 给容器分配新的内容，并替换当前内容，将动态链表 b 的长度设置为 3 ，并将值设置为 1.0f
    b.assign(3,1.0f);// {1.0f, 1.0f, 1.0f}
    // 给容器分配新的内容，传入两迭代器并将[a.begin(),a.end()] (a同为双向链表)赋值给 b 
    b.assign(a.begin(),a.end());
    // 给容器分配新的内容，并将 {1.0f,2.0f,3.0f} 赋值给 b
    b.assign({1.0f,2.0f,3.0f});

    // 将双向链表 a 和 b 进行替换
    b.swap(a);
    std::swap(a,b);
```

## 元素访问
```cpp
    //元素访问，不能随机访问
    //b[0];
    //b.at(0);

    // 返回第一个元素，不是迭代器
    b.front();
    //b.back();

    // 返回第五个元素 ps: iter不能为 const
    std::advance(iterBegin,4); // better
    // 返回第五个元素 ps: 需要有返回值
    auto iter5 = std::next(iterBegin,4);
```

## 迭代器相关
```cpp
    //声明迭代器
    std::list<float>::iterator iter1;
    //std::list<float>::reverse_iterator iter2;
    std::list<float>::const_iterator iter3;
    //std::list<float>::const_reverse_iterator iter4;
    //迭代器相关
    a.begin();
    a.end();
    //const
    a.cbegin();
    a.cbegin();
    //尾部->头部
    //a.rbegin();
    //a.rend();
    //const 尾部->头部
    //a.crbegin();
    //a.crend();

    // 头部前一个地址
    a.before_begin();
    a.cbefore_begin();// const
```

## 增删
```cpp
    //b.push_back(10.0f);
    //a.pop_back(); 
    //if(!a.empty()) a.pop_back();
    
    // 头部插入一个元素
    b.push_front(1.2f);
    b.emplace_front(1.3f);
    // 尾部插入元素
    auto iter = b.insert_after(b.before_begin(),100.0f);
    iter = b.insert_after(b.before_begin(),10,-10.0f);
    b.insert_after(b.before_begin(),h.begin(),h.end());

    //b.erase(b.begin());
    //删除第0个元素
    b.erase_after(b.before_begin()); //return void
    //删除第1个元素
    b.erase_after(b.begin()); //return void
    // 删除(b.being(),b,end()]元素;
    b.erase_after(b.begin(),b.end()); //return void

```

## 异常

```cpp

```

## 特殊

```cpp
    b.remove(1.0f);
    b.remove_if([](auto v) { return v > 100.0f;});
    b.reverse(); // 1 2 3 4 -> 4 3 2 1
    b.sort(); // <
    g.sort();
    b.merge(g);
    c.unique();
    c.splice_after(c.before_begin(),b);
    
    //插入元素操作
    Group forlist = {1,2,3,4,5};
    auto fiter = forlist.before_begin();
    for(int i = 0;i<2;++) ++ fiter;
    forlist.insert_after(fiter,10);
```