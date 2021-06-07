---
title: CppPrimer5th-Chapter11
mathjax: true
date: 2021-02-06 23:13:25
tags:
- C++
categories: C++
---
《C++ Primer 5th》第十一章中的重要知识点，这一章的内容是介绍关联容器，map、set以及相关的变体。
<!-- more -->

## 分类
从三个方面分类关联容器
1. map或者set
2. 是否允许重复key（multi）
3. key的顺序是否有规律（unordered）

```cpp
map // 保存key-value
set // 只保存key
multimap
multiset
unordered_map
unordered_set
unordered_multimap
unordered_multiset

map<string, size_t> word_count={{"ad", 100},
                                {"top", 80}};
set<string> word_bag(vec.begin(), vec.end());
for(auto &w : word_count)
    cout<< w.first << w.second; // first成员为const类型
```
>关联容器不支持顺序容器的位置相关的操作，例如push_front或push_back。原因是关联容器中元素是根据关键字存储的，这些操作对关联容器没有意义。而且，关联容器也不支持构造函数或插入操作这些接受一个元素值和一个数量值的操作。

## pair类型
定义在头文件 *utility* 中
一个pair保存两个数据成员。类似容器，pair是一个用来生成特定类型的模板。当创建一个pair时，我们必须提供两个类型名，pair的数据成员将具有对应的类型。两个类型不要求一样：
pair使用public成员first与second来存储key与value

## 关联容器额外的类型

- key_type
- mapped_type
- value_type

对map和set来说这三个类型含义有所不同，具体参照以下

```cpp
set<string>::key_type v1; // string
set<string>::value_type v2; //string
map<string, int>::key_type v3; //string
map<string, int>::value_type v4; // pair
map<string, int>::mapped_type v5; // int
```
## 关联容器迭代器

虽然set类型同时定义了iterator和const_iterator类型，但两种类型都只允许只读访问set中的元素。与不能改变一个map元素的关键字一样，一个set中的关键字也是const的。可以用一个set迭代器来读取元素的值，但不能修改

由于关联容器的迭代器大部分都是只读const，并且key值排列经常没有顺序，因此**泛型算法通常不会作用在关联容器上**。关联容器通常只会作为算法的源或者目的容器，而不参与过多的元素变换过程。

## map的下标操作
map元素的下标访问有两种访问方式，set因为只有key，没有下标访问操作。
```cpp
m[k]; // 若k不存在，则在m中生成k项
m.at(k); // 若k不存在，抛出out of range异常
```
如果只是想查找k在不在，不应该使用下标操作，因为若没有这个元素，使用下表会造成元素插入。查找元素应该使用find

```cpp
if(m.find(k) == m.end()) // find由前至后的寻找k，找不到则返回尾后迭代器
    cout<< "k is not in the map";
```
