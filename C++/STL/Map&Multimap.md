<!--
 * @Author: your name
 * @Date: 2021-11-18 16:25:13
 * @LastEditTime: 2022-03-31 11:05:43
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /workspace/Blog/C++/STL/Map&Multimap.md
-->

# Map&Multimap

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

Map, Multimap是C++98中引入的二叉树数据结构

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
    
    // 可以通过[]访问value
    // auto == std::string
    auto& info = b[10];
    // [] -> auto iter = b.insert(std::make_pair(13,std::string())).first; return (*iter).second;
    b[13] = "val";

    // at
    try {
        auto& findInfo = b.at(10); //const
    } catch(...){
        
    }

    // find
    auto findIter = b.find(10);
    if(findIter != std::end(b) /* b.end() */){
        //std::pair<int, std::string> obj;
        //obj = *findIter; //not good
        //*findIter; // std::pair<const int ,std::string>&
        auto& v = (*findIter).second;
    }else{
        // not finded
    }
    
    //返回key存在个数
    //map count 0/1
    //multimap count >= 0
    auto num = a.count(1);
    
    //返回[first,end)第一个>=值val的位置
    auto lower = a.lower_bound(1);
    if(lower != a.end()) {
        if(*lower.first == 1){
            // has 1
        }
    }
    //返回[first,end)第一个>值val的位置
    auto high = a.upper_bound(1);
    
    auto range = a.equal_range(1); // return std::make_pair(a.lower_bound(1),a.upper_bound(1));

```

### find模板类用法
```cpp
auto info =get_default(b,10);

template <class Map>
typename Map::mapped_type get_default(const Map &map, const typename Map::key_type &key,const typename Map::mapped_type &dflt = typename Map::mapped_type()){
        auto pos = map.find(key);
        return (pos != map.end() ? pos->second : dflt);
}

void testGetDefault(){
    auto info =get_default(b,10,"error");

    auto info2 = get_default(c,11);
    if(info2.empty()){
        
    }else{

    }
}
```

## 迭代器相关
```cpp
    std::map<int,std::string>::iterator iter1;
    
    //迭代器相关
    a.begin();
    a.end();
    auto iterBegin = a.begin();
    for(auto it = a.begin();it != a.end();it++){
        //...
    }
    //const
    a.cbegin();
    a.cbegin();
    //尾部->头部
    a.rbegin();
    a.rend();
    auto riterBegin = a.rbegin();
    // !=
    for(auto rit = a.rbegin();rit != a.rend();rit++){
        //...
    }
    //const 尾部->头部
    a.crbegin();
    a.crend();
```

## 增删

```cpp
    // 删除指定元素
    auto eraserIter = b.erase(b.begin());
    // 删除两迭代器中间的值
    eraseIter = b.erase(b.begin(),b.end());

    // 插入指定元素
    auto state = b.insert(std::make_pair(100,"good")); //state is a pair<Iterator bool>
    // 将另一map/multimap插入到该map/multimap中
    auto insertIter = b.insert(c.begin(),c.end());
    
    // 插入元素
    b.emplace(std::make_pair(10,"has it"));
    b.emplace(11,std::string("again")); //b.emplace(std::pair<const int,std::string>(10,std::string("agian")));
    b.emplace(12,"third"); // b.emplace(std::pair<const int,std::string>(10,"third"));
    b.emplace_hint(b.end(),13,"haha");
```

## 异常

## 特殊