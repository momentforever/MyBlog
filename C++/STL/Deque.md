<!--
 * @Author: your name
 * @Date: 2021-11-02 15:51:22
 * @LastEditTime: 2022-03-31 13:45:57
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /workspace/Blog/C++/STL/Deque.md
-->
# Deque

## 说明


### 引用库

```cpp
namespace std {
    template<typename T,typename Allocator = allocator<T>>
    class deque;
}
```

### 本质

``连续存储结构``，即其每个元素在内存上也是连续的，类似于vector，不同之处在于， deque提供了两级数组结构， 第一级完全类似于vector，代表实际容器；另一级维护容器的首位地址。这样，deque除了具有vector的所有功能外， 还支持高效的首/尾端插入/删除操作。

### 特点

随机访问元素，末端和头部添加删除元素效率高。中间删除和添加元素效率低，元素的存放和迭代比vector要慢。迭代器不能是普通的指针。

## 创建与初始化

```cpp
    // 直接声明一个双向队列
    std::deque<float> a;
    // 将双向队列 a 复制给双向队列 b (值复制)
    std::deque<float> b = a;
    std::deque<float> b(a);
    // 创建一个长度为10的双向队列，并将所有元素初始化为0.0f(默认)
    std::deque<float> d(10);
    // 创建一个长度为10的双向队列，并将所有元素初始化为1.0f
    std::deque<float> e(10,1.0f);
    // 传入迭代器初始化双向队列
    std::deque<float> f(e.begin(),e.end());
    // 以填入方式初始化双向队列
    std::deque<float> g({1.0f,2.0f,3.0f});

    std::deque<float> h = {1.0f,3.0f,4.0f};
    
```

## 基础属性
```cpp
    // 判断是否为空
    d.empty();
    // 返回长度
    d.size();
    // 返回最大容量
    // 为INT_MAX/2
    d.max_size();
    
    // c++11
    // 使内存空间恰好符合容量
    d.shrink_to_fit();
    // d.size() == 10
    d.resize(10);
    // b.size() == 100
    // 所有初始值为 1.0f
    d.resize(100,1.0f);
    // 清空数组
    d.clear(); // notice
```

### Note

```cpp
    // 和vector不同，deque不提供以下的函数
    // d.capacity();
```

## 赋值

```cpp
    // 直接将双向队列 a 赋值给动态数组 b 
    b = g;
    // 将双向队列 b 的长度设置为 3 ，并将值设置为1.0f
    b.assign(3,1.0f);
    // 将双向队列 b 初始化，将[a.begin(),a.end()]赋值给 b
    b.assign(a.begin(),a.end());
    // 将双向队列 b 初始化，并将{1.0f,2.0f,3.0f}赋值给 b 
    b.assign({1.0f,2.0f,3.0f});
    // 将双向队列 a 和 b 进行替换
    b.swap(a);
    std::swap(a,b);
```

## 元素访问

```cpp
    // 直接访问
    b[0];
    // 直接访问
    b.at(0);
    
    // 返回第一个元素，不是迭代器
    b.front();
    // 返回最后一个元素，不是迭代器
    b.back();
```

## 迭代器相关

```cpp
    //声明迭代器
    std::deque<float>::iterator iter1;
    std::deque<float>::reverse_iterator iter2;
    std::deque<float>::const_iterator iter3;
    std::deque<float>::const_reverse_iterator iter4;
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
```cpp
    // 尾部添加元素
    b.push_back(10.0f);
    // 尾部添加元素，直接在容器尾部创建这个元素，C++11
    b.emplace_back(1.3f);
    // 尾部弹出一个元素，ps: 没有返回值
    b.pop_back();
    // 头部插入一个元素
    b.push_front(1.2f);
    // 头部插入一个元素，直接在容器头部创建这个元素，C++11
    b.emplace_front(1.3f);
    // 头部弹出一个元素，ps: 没有返回值
    b.pop_front();

    // 插入一个元素
    b.insert(b.end(),100.0f);
    // 插入n个元素
    b.insert(b.end(),10,-10.0f);
    // 插入一个元素，直接在迭代器位置创建这个元素，C++11
    b.emplace(b.end(),10.0f);
```

## 异常
```cpp
    // 异常
    // push_back push_front
    // 元素 move/copy 没有异常的话， insert emplace emplace_back emplace_front
    // pop_back pop_front
    // 元素 move/copy没有异常的话，erase
    // swap clear
```
## 特殊
```cpp
static void exampleDeque(){
    using Buffer = std::vector<char>;
    using BufferGroup = std::deque<Buffer>;
    BufferGroup group;
    Buffer buffer;
    auto ok = readFromClient(socket,&buffer);
    if(ok) {
        group.emplace_back(std::move(buffer));
    }else {
        // handle error
    }

    while(!group.empty()){
        auto ok =sendToClient(socket,group.front());
        if(ok){
            group.front();
        }else{
            //handle error
            break;
        }
    }
}
```