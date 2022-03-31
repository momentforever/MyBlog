<!--
 * @Author: your name
 * @Date: 2022-03-31 11:42:37
 * @LastEditTime: 2022-03-31 15:16:01
 * @LastEditors: Please set LastEditors
 * @Description: 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
 * @FilePath: /workspace/MyBlog/C++/STL/Set&Multiset.md
-->
# Set&Multiset

## 说明

### 引用库

```cpp
#include <set>

namespace std{
    template<typename T,typename Compare = less<T>,typename Allocator = allocator<T>>
    class set;

    template<typename T,typename Compare = less<T>,typename Allocator = allocator<T>>
    class multiset;
}
```

### 本质

``set``, ``multiset``是C++98中引入的二叉树数据结构。通常容器中元素会进行频繁插入，删除，查找，并且会自动顺序。

### 特点

+ 自动将元素排序。
+ 插入和删除查找O(log(n))。
+ 元素必须支持严格的弱顺序。

1. x < y == true, y < x == false
2. x < y == true, y < x == true,x < z == true
3. x < x === false
4. a== b, b == c, c == a

## 创建与初始化

```cpp
    std::set<float> a;
    std::set<float> b = a;
    std::set<float> c(a);
    std::set<float> d(c.begin(),c.end());
    std::set<float> g({1.0f,4.0f,3.0f});
    std::set<float> h = {1.0f, 3.0f, 4.0f};
```

## 基础属性

```cpp
    // 判断是否为空
    d.empty();
    // 返回长度
    d.size();
    // 返回可扩容的最大容量
    d.max_size();

    //special set中key的值就是value的值，所以用途相同
    //比较key值大小
    auto keytcomp = c.key_comp();
    //比较value值大小
    auto valuecomp = c.value_comp();
```

## 赋值

```cpp
    // std::set<float> b;
    // std::set<float> g;
    b = g;

    // 将二叉树 a 和 b 进行替换
    b.swap(a);
    std::swap(a,b);
```

## 元素访问

```cpp
    // find
    auto findIter = b.find(10.0f);
    if(findIter != std::end(b) /* b.end() */){
        // finded
    }else{
        // not finded
    }
    
    //返回key存在个数
    //set count 0/1
    //multiset count >= 0
    auto num = a.count(1);
    
    //返回[first,end)第一个>=值val的位置
    auto lower = a.lower_bound(1.0f);
    if(lower != a.end()) {
        if(*lower == 1.0f){
            // has 1
        }
    }
    //返回[first,end)第一个>值val的位置
    auto high = a.upper_bound(1);
    
    auto range = a.equal_range(1); // return std::make_pair(a.lower_bound(1),a.upper_bound(1));
```

## 迭代器相关

```cpp
    std::set<float>::iterator iter1;
    
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
    auto state = b.insert(100.0f); //state is a pair<Iterator bool>
    // 将另一set/multiset插入到该set/multiset中
    auto insertIter = b.insert(c.begin(),c.end());
    
    // 插入元素
    b.emplace(100.0f);
    b.emplace_hint(b.end(),11.0f);
```

## 异常

## 特殊

## 例子

```cpp
#include <iostream>
#include <set>
#include <string>

class Person {
private:
	int m_age;
	std::string m_name;
public:
	Person(int a, std::string n) :m_age{ a }, m_name{ std::move(n) } {}
	int age() const { return m_age; }
	const std::string& name() const { return m_name; }
	void print() const { std::cout << m_age << " : " << m_name << std::endl; }
	void changeAge(int newAge) { m_age = newAge; }
	bool operator == (Person const& rhs)const {
		return m_age == rhs.age() && m_name == rhs.name();
		//return m_age == rhs.m_age && m_name == rhs.m_name;
	}
};
struct CompareAge
{
	template<typename T>
	bool operator()(const T& t1, const T& t2) const {
		return t1.age() < t2.age();
	}
};
struct CompareName
{
	template<typename T>
	bool operator()(const T& t1, const T& t2) const {
		return t1.name() < t2.name();
	}
};

int main()
{
	Person a(14, "Alice");
	Person b(25, "Bob");
	Person c(17, "Camily");

	// first we want to put into a set for their age
	// second use name
	std::cout << "order of age:\n";
	using Group = std::set<Person, CompareAge>;
	Group g;
	g.insert(a);
	g.insert(b);
	g.insert(c);
	for (auto& v : g) {
		v.print();
	}
	std::cout << "\n";

	auto ageIter = g.begin();
	//(*ageIter).changeAge(30);

	std::cout << "order of name:\n";
	using Group2 = std::set<Person, CompareName>;
	Group2 g2;
	g2.insert(a);
	g2.insert(b);
	g2.insert(c);
	for (auto& v : g2) {
		v.print();
	}

	auto iter = g2.find(a);
	if (iter == g2.end()) {
		std::cout << "not find\n";
	}
	else {
		//finded
		std::cout << "find\n";
	}

	Person aa(25, "Alice");
	
	auto iter = g2.find(aa);
	if (iter == g2.end()) {
		std::cout << "not find(set)\n";
	}
	else {
		//finded
		std::cout << "find(set)\n";
	}

	// ==
	iter = std::find(g2.begin(), g2.end(), aa);
	if (iter == g2.end()) {
		std::cout << "not find(==)\n";
	}
	else {
		//finded
		std::cout << "find(==)\n";
	}
}
```