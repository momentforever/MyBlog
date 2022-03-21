<!--
 * @Author: your name
 * @Date: 2021-11-15 09:21:12
 * @LastEditTime: 2021-11-22 14:21:34
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /workspace/Blog/C++/STL/List.md
-->
# List

## 说明

### 引用库

```cpp
#include <list>
namespace std {
    template<typename T,typename Allocator = allocator<T>>
    class list;
}
```

### 本质

非连续存储结构，具有双链表结构，每个元素维护一对前向和后向指针，因此支持前向/后向遍历。 支持高效的随机插入/删除操作，但随机访问效率低下，且由于需要额外维护指针 ，开销也比较大。每一个结点都包括一个信息快Info、一个前驱指针Pre、一个后驱指针Post。可以不分配必须的内存大小方便的进行添加和删除操作。使用的是非连续的内存空间进行存储。

### 特点

+ 不支持随机访问元素，访问头部和尾部速度快。
+ 任何位置插入删除元素都很快，常量时间内完成。
+ 插入和删除不会造成迭代器失效。
+ 对于异常支持的好，出现异常对于list而言，要么不成功，要么什么影响没有。

## 创建与初始化

```cpp
    // 直接声明一个双向链表
    std::list<float> a;
     // 将双向链表 a 复制给双向链表 b (值复制)
    std::list<float> b = a;
    std::list<float> c(a);
    // 创建一个长度为10的双向链表，并将所有元素初始化为0.0f(默认)
    std::list<float> d(10);
    // 创建一个长度为10的双向链表，并将所有元素初始化为1.0f
    std::list<float> e(10,1.0f);
    // 传入迭代器初始化双向链表
    std::list<float> f(e.begin().e.end());
    // 以填入方式初始化双向链表
    std::list<float> g({1.0f,2.0f,3.0f});
    std::list<float> h = {1.0f,3.0f,4.0f};
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
    // 不能随机访问
    // b[0];
    // b.at(0);
    
    // 返回第一个元素，不是迭代器
    b.front();
    // 返回最后一个元素，不是迭代器
    b.back();

    // 返回第五个元素 ps: iter不能为 const
    std::advance(iterBegin,4); // better
    // 返回第五个元素 ps: 需要有返回值
    auto iter5 = std::next(iterBegin,4);
```

## 迭代器相关
```cpp
    //声明迭代器
    std::list<float>::iterator iter1;
    std::list<float>::reverse_iterator iter2;
    std::list<float>::const_iterator iter3;
    std::list<float>::const_reverse_iterator iter4;
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

    // 头部前一个地址
    a.before_begin();
    a.cbefore_begin();
```

### Note

```cpp
    //迭代器寻找第五个元素
    assert(a.size()>=10);
    for(int i = 0;i<5;++i)++iterBegin;

    // 遍历所有元素
    for(auto iter=a.begin();a!=a.end();++iter);
    //iter没有 < > <= => 操作符
```

## 增删

```cpp
    // 尾部插入一个元素
    b.push_back(10.0f);
    // 尾部添加元素，直接在容器尾部创建这个元素，C++11
    b.emplace_back(1.3f);
    // 尾部弹出一个元素，ps: 没有返回值
    // 当动态数组为空时，直接报错
    b.pop_back();

    // 头部插入一个元素
    b.push_front(1.3f);
    // 头部插入一个元素，直接在容器头部创建这个元素，C++11
    b.emplace_front(1.3f);
    // 头部弹出一个元素，ps: 没有返回值
    b.pop_front();

    // 在 b.end() 插入 1个 100.0f 元素，并返回指针
    auto iter = b.insert(b.end(),100.0f);
    // 在 b.end() 插入 10个 -10.0f 元素，并返回头指针
    iter = b.insert(b.end(),10,-10.0f);
    // 传入两迭代器，在b.end()插入[a.begin(),a.end()] (a同为双向链表)
    iter = b.insert(b.end(),h.begin(),h.end());

    // d.size() == 10
    b.resize(10);
    // b.size() == 100
    // 所有初始值为 1.0f
    b.resize(100,1.0f);
```

## 异常

```cpp

```

## 特殊

```cpp
    b.remove(1.0f);
    b.remove_if([](auto v){return v > 100.0f});
    b.reverse(); // 1 2 3 4 -> 4 3 2 1
    b.sort(); // <
    //std::sort(a.begin(),a.end()); // list is error
    g.sort();
    b.merge(g); // b <- g
    c.unqiue(); // 1 1 2 2 1 1 3 4 -> 1 2 1 3 4
    c.splice(c.begin(),b);
```