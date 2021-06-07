---
title: CppPrimer5th-Chapter4
mathjax: true
date: 2021-01-27 22:52:52
tags:
- C++
- 基础知识
categories: C++
---
《C++ Primer 5th》第四章中的重要知识点，第四章内容为表达式

<!-- more -->

## 左值与右值

当一个对象被用作右值的时候，用的是对象的值（内容）；当对象被用作左值的时候，用的是对象的身份（在内存中的位置）。
一个原则：
- 在需要右值的地方可以用左值来代替，使用的是对象的值
- 但是反过来，不能把右值当成左值

## 运算符

C++11中，对于除法，不管是正数除以正数，还是负数除以负数，在无法除尽的时候都向0舍入。

赋值运算符满足右结合律，所以可以出现连续多个变量赋值
```cpp
int a,b;
a = b = 0;

++a; //养成使用前置版本递增运算符的习惯，对于迭代器等复杂的运算结构，可以节省不必要的开销
```
### 混用解运算符和递增

```cpp
auto pbeg = v.begin();
while(pbeg != v.end())
    cout << *pbeg++ << endl;
```
递增运算符的优先级高于解运算符，等价于先移动迭代器，再解出原位置的值（因为后置递增运算符返回原值），**这种写法是C++常见并推荐的写法**。

## sizeof

sizeof运算符返回的是size_t类型的常量表达式

- 对string对象或vector对象执行sizeof运算只返回该类型固定部分的大小，不会计算对象中的元素占用了多少空间。
- 对解引用指针执行sizeof运算得到指针指向的对象所占空间的大小，指针不需有效
- 对char或者类型为char的表达式执行sizeof运算，结果得1
- 对数组执行sizeof运算得到整个数组所占空间的大小，等价于对数组中所有的元素各执行一次sizeof运算并将所得结果求和。注意，sizeof运算不会把数组转换成指针来处理。

```cpp
int main() {
	int ia[3][4] = {
		{1,2,3,4},
		{5,6,7,8},
		{9,10,11,12}
	};
	auto p = ia;
	cout << sizeof p << endl; // 8
    cout << sizeof * p << endl; //16
	cout << sizeof * ia << endl; //16
    cout << sizeof ia << endl; // 48
	return 0;
}
```
## 类型转换

### 若干隐形转换

算术转换，一般性原则是占内存地址小的类型转化为占内存地址大的类型，即char bool可以转换为int，int转换为long和double等

**数组转换成指针**：在大多数用到数组的表达式中，数组自动转换成指向数组首元素的指针：
当数组被用作decltype关键字的参数，或者作为取地址符（&）、sizeof及typeid等运算符的运算对象时，上述转换不会发生。

**指针的转换**
- 0和nullptr转换为任意指针类型
- 任意非常量的指针能转换成void＊
- 指向任意对象的指针能转换成const void＊

### 显性转换

本质上强制转换是一种危险的操作
标准形式 *cast-name<name>(expression)*

- static_cast,任何具有明确定义的类型转换，只要不包含底层const，都可以使用static_cast
```cpp
double slope = static_cast<double>(j);

// 可以使用static_cast找回存在于void＊指针
void *p = &d;
double *dp = static_cast<double *>(p);

```
强制类型转换告诉程序的读者和编译器：我们知道并且不在乎潜在的精度损失

- const_cast, 强行将一个只读const对象变成可写状态
```cpp
// cast away the const
const char *pc;
char *p = const_cast<char*>(pc);
```
const_cast只能解决常量转换，不能进行其他类型转换

- reinterpret_cast *为运算对象的位模式提供较低层次上的重新解释?*

我们必须牢记pc所指的真实对象是一个int而非字符，如果把pc当成普通的字符指针使用就可能在运行时发生错误。

```cpp
int *ip;
char *pc = reinterpret_cast<char*>(ip);
// dangerous
string str(pc);
```
