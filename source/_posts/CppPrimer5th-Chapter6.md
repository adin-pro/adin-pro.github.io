---
title: CppPrimer5th-Chapter6
mathjax: true
date: 2021-01-28 16:22:00
tags:
- C++
- 基础知识
categories: C++
---

《C++ Primer 5th》第六章中的重要知识点，第六章内容为函数

<!-- more -->

## const形参和实参

使用实参初始化形参的时候会忽略**顶层const**，也就说即便形参要求的格式为常量形参，也可以使用非常量形参为其赋值。
函数重载的时候，不能将顶层const和非const形参视为重载
```cpp
void fcn(const int i) {}
void fcn(int i){} // wrong: top-level const

void fcn0(int* p);
void dcn0(int* const p); //wrong: top-level const

void fcn1(const int* p);
void fcn1(int* p); //right: low-level const

void fcn2(int& r);
void fcn2(const int& r); //right: low-level const

```
我们可以使用非常量初始化一个底层const对象，但是反过来不行；同时一个普通的引用必须用同类型的对象初始化。
```cpp
int i=42;
// right
const int *cp = &i;
const int &r = i;
const int &r2 = 42;
// wrong
int *p = cp;
int &r3 = r;
int &r4 = 42;
```
一般将引用形参设置为const，否则直接以字面值无法传递给形参（非const引用不能引用无地址的字面值）

### 对于数组的引用

```cpp
int *matrix[10];
int (*matrix)[10];
```
## 含有可变形参的函数

### initialize_list形参
initializer_list是一种标准库类型，用于表示某种特定类型的值的数组，是一种模板类型


### 省略符形参
省略符形参是为了便于c++函数访问部分特殊c代码出现的，也就是说并非c++推荐的格式

## 返回
>C++11新标准规定，函数可以返回花括号包围的值的列表。类似于其他返回结果，此处的列表也用来对表示函数返回的临时量进行初始化。如果列表为空，临时量执行值初始化；否则，返回的值由函数的返回类型决定

在下面的函数中，我们返回一个vector对象，用它存放表示错误信息的string对象：
```cpp
vector<string> process(){
    // ...
    if (expected.empty())
        return {};
    else if (expected == actual)
        return {"FunctionX", "Okay"};
    else
        return {"FunctionX", expected, actual};
}
```
使用花括号的内容去初始化vector，然后返回这个vector

### 返回数组指针
因为数组不能被拷贝，所以函数不能返回数组,不过，函数可以返回数组的指针或引用.
从语法上来说，要想定义一个返回数组的指针或引用的函数比较烦琐，但是有一些方法可以简化这一任务，其中最直接的方法是使用类型别名
```cpp
using arrT = int[10];
arrT * func(int i);
等价于一种很复杂的声明
int (*func(int i))[10];
```

C++11中有了新的返回数组的方式
1. 返回尾置类型
```cpp
auto func(int i) -> int(*)[10];
```
2. 使用decltype
```cpp
int odd[]={1,3,5};
int even[]={2,4,6};

decltype(odd) * func(int i);
```
有一个地方需要注意：decltype并不负责把数组类型转换成对应的指针，所以decltype的结果是个数组，要想表示arrPtr返回指针还必须在函数声明时加一个＊符号。



