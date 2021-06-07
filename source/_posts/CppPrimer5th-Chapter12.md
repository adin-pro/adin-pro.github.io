---
title: CppPrimer5th-Chapter12
mathjax: true
date: 2021-02-07 10:52:21
tags:
- C++
categories: C++
---
《C++ Primer 5th》第十二章笔记内容，有关智能指针和动态内存的管理。
<!-- more -->

程序相关的对象内存分为三部分，static空间，栈空间stack，堆空间heap。
- static空间中的对象在程序运行前生成，程序结束时销毁
- stack栈空间里的对象在程序块作用域中存在，超出作用域后销毁
- 上述两者都是编译器负责自动创建和销毁
- heap空间由程序负责分配和销毁，处理不好会造成内存泄漏，这种内存分配也叫做动态内存管理

**动态内存的管理十分重要且棘手**

## 动态内存与智能指针

C++中使用一对运算符 new 和 delete 来分配和销毁对象内存
C++11引入了智能指针shared_ptr unique_ptr weak_ptr , 使用智能指针可以降低动态内存管理的难度

### shared_ptr
是一个模板类，按照模板创建的方式来创建智能指针
```cpp
shared_ptr<string> p1;
shared_ptr<list<int>> p2;

usage：
if(p1 && !p1->empty())
    *p1 = "hi";

shared_ptr独有的操作：
p1.use_count(); // 返回指向共享对象的智能指针数量，返回速度较慢
p1.unique(); // 判断是否只有一个指针指向对象
make_shared<T>(args); // 初始化shared_ptr并用args初始化
p1 = p3; //p3的引用计数减少，p1的引用计数增加
```
>最安全的分配和使用动态内存的方法是调用一个名为make_shared的标准库函数。此函数在动态内存中分配一个对象并初始化它，返回指向此对象的shared_ptr。与智能指针一样，make_shared也定义在头文件memory中。

计数器清零时，对象就会被自动释放（call destructor），不能也不用delete

> 使用动态内存的一个常见原因是允许多个对象共享相同的状态。

运用shared_ptr与initialize_list的一个例子

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <memory>

using namespace std;

class StrBlob {
public:
	typedef std::vector<string>::size_type size_type;
	StrBlob();
	StrBlob(initializer_list<string> il);
	size_type size() const { return data->size(); }
	bool empty() const { return data->empty(); }
	void push_back( const string &t ){data->push_back(t); }
	void pop_back();
	string & front();
	string & back();
private:
	shared_ptr < vector<string> >data;
	void check(size_type i, const string &msg) const;
};

StrBlob::StrBlob():data(make_shared<vector<string>>()){}
StrBlob::StrBlob(initializer_list<string> il): data(make_shared<vector<string>>(il)){}
void StrBlob::check(size_type i, const string &msg) const {
	if (i > data->size())
		throw out_of_range(msg);
}

string & StrBlob::front() {
	check(0, "front on empty StrBlob");
	return data->front();
}

string & StrBlob::back() {
	check(0, "back on empty StrBlob");
	return data->back();
}
```
