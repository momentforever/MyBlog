<!--
 * @Author: your name
 * @Date: 2022-03-31 11:40:08
 * @LastEditTime: 2022-03-31 14:18:32
 * @LastEditors: Please set LastEditors
 * @Description: 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
 * @FilePath: /workspace/MyBlog/C++/STL/Unordered_map&Unordered_multimap.md
-->
# Unordered_map&Unordered_multimap

## 说明

### 引用库

```cpp
#include <unordered_map>

namespace std{
    template<typename Key,typename T,typename Hash = hash<Key>,typename EqPerd = equal_to<Key>,typename Allocator = allocator<pair<const Key, T>>>
    class unordered_map;
    template<typename Key,typename T,typename Hash = hash<Key>,typename EqPred = equal_to<Key>, typename Allocator = allocator<pair<const Key, T >>>
    class unordered_multimap;
}
```

### 本质

``unordered_map``, ``unordered_multimap``是C++11中以hash table(哈希表)为基础的，内部元素没有明确的顺序的容器。


### 特点

## 创建与初始化

## 基础属性

## 赋值

## 元素访问

## 迭代器相关

## 增删

## 异常


## 特殊

### 重载hash函数
```cpp
#include <iostream>
#include <algorithm>
#include <string>
#include <unordered_map>

class Position {
public:
	int x() const { return m_x; }
	int y() const { return m_y; }
	Position(int _x, int _y) : m_x(_x), m_y(_y) {}
private:
	int m_x = 0;
	int m_y = 0;
};

//boost
template <class	T> inline void hash_combine(std::size_t& seed, const T& v) {
	std::hash<T> hasher;
	seed ^= hasher(v) + 0x9e3779b9 + (seed << 6) + (seed >> 2);
}

namespace std {
	//template<typename T>struct hash {
	//};

	template<> struct hash<Position> {
		size_t operator() (const Position& p) const {
			//值尽可能唯一
			//return p.x() + p.y(); // not good  2 => (2,0) (1,1) (2,0)
			//return hash<int>()(p.x()) ^ hash<int>()(p.y());
			auto key = hash<int>()(p.x());
			hash_combine(key, p.y());
			return key;
		}
	};
}
class Person {
private:
	int m_age;
	std::string m_name;
public:
	Person(int a, std::string n) : m_age{ a }, m_name{ std::move(n) } {}
	int age() const { return m_age; }
	const std::string& name() const { return m_name; }
	void print() const { std::cout << m_age << " : " << m_name << std::endl; }
	void changeAge(int newAge) { m_age = newAge; }
	bool operator == (Person const& rhs) const {
		return m_age == rhs.m_age && m_name == rhs.m_name;
	}
};

void testUnorderedMap() {
	using Group = std::unordered_map<Position, Person>;
	//using Group = std::unordered_map<Position, Person ,Hasher<Position>>;
	Group group;
}

struct CompareAge
{
	template<typename T>
	bool operator()(const T& t1, const T& t2) const {
		return t1.age() < t2.age();
	}
};

int main() {
	testUnorderedMap();
}
```

