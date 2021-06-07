---
title: CppPrimer5th-Chapter7
mathjax: true
date: 2021-01-29 09:56:43
tags:
- C++
- 基础知识
categories: C++
---

《C++ Primer 5th》第七章中的重要知识点，第七章内容为类

<!-- more -->

## 类的定义

**定义在类内部的函数是隐式的成员函数**

### const成员函数

```cpp
std::string isbn() const {return bookNo;}
std::string isbn() const {return this->bookNo;} // same
/***************************/
调用total.isbn()等价于
Sales_data::isbn(&total); // fake code

```
this是一个常量指针，不允许改变this的值，指向调用成员函数的对象

*const作用是修改隐式指针const的类型*, const是常量指针（Sales_data * const），并非指向常量的指针，因此无法指向常量对象，有时候就需要将const转换为指向常量的指针形式(const Sales_data * const)，const是隐式指针，无法直接修改类型，因此借助const成员函数的帮助

>C++语言的做法是允许把const关键字放在成员函数的参数列表之后，此时，紧跟在参数列表后面的const表示this是一个指向常量的指针

因为this是指向常量的指针，所以常量成员函数不能改变调用它的对象的内容

### 返回this对象的成员函数
完成类似于自增的函数combine，返回this对象
```cpp
Sales_data& Sales_data::combine(const Sales_data& rhs){
    units_sold += rhs.units_sold;
    revenue += rhs.revenue;
    return *this;
}
```
>该函数一个值得关注的部分是它的返回类型和返回语句。一般来说，当我们定义的函数类似于某个内置运算符时，应该令该函数的行为尽量模仿这个运算符。内置的赋值运算符把它的左侧运算对象当成左值返回（参见4.4节，第129页），因此为了与它保持一致，combine函数必须返回引用类型（参见6.3.2节，第202页）。因为此时的左侧运算对象是一个Sales_data的对象，所以返回类型应该是Sales_data&。

### IO类不支持拷贝
调用的时候需要直接使用对象引用。
```cpp
istream& read(istream& os, Sales_data& item){
    os >> item.bookNo >> item.units_sold >> price;
    item.revenue = price * item.units_sold;
    return is;
}
```

## 构造函数

>在C++11新标准中，如果我们需要默认的行为，那么可以通过在参数列表后面写上= default来要求编译器生成构造函数。其中，= default既可以和声明一起出现在类的内部，也可以作为定义出现在类的外部。和其他函数一样，如果= default在类的内部，则默认构造函数是内联的；如果它在类的外部，则该成员默认情况下不是内联的。
```cpp
struct Sales_data{
    Sales_data() = default;
    Sales_data(const std::string &s):bookNo(s){};
};
```

## 进阶属性
```cpp
class Screen {
public:
	typedef string::size_type pos; // is equivalent to: using pos = string::size_type;
	Screen() = default; // default constructor
	Screen(pos h, pos w, char c) : height{ h }, width{ w }, contents(h* w, c){};
	char get() { return contents[cursor]; } // implicit inline
	inline char get(pos h, pos w) const; // explicit inline ; overloaded function
	Screen& move(pos r, pos c);
    Screen& set(char c); // return *this
    Screen& set(pos, pos, char); // return *this
private:
	pos cursor = 0;
	pos height = 0, width = 0;
	string contents;
    mutable size_t access_ctr = 0;  // mutable variable
};

void Screen::some_member() const {  // 
	++access_ctr;
}
```
通过返回*this（Screen对象引用）可以实现move，set函数的序列化调用
```cpp
myScreen.move(4,0).set('#');
```

*Warning: 一个const成员函数如果以引用的形式返回＊this，那么它的返回类型将是常量引用。*

$\color{red}{常量对象，以及常量对象的引用或指针都只能调用常量成员函数。}$

一些成员函数需要有const和非const重载版本，来满足不同对象的要求。

## 构造函数初始化顺序

一般而言，要求构造初始化类成员的时候按照定义顺序来，尤其是存在类成员变量互相初始化的时候。
```cpp
class Exp{
public:
    int i;
    int j;
    Exp(int val): j{val}, i{j} {} // wrong initialize i with unintialized value
}
// i会按照定义的顺序先执行初始化，而不是按照列表初始化里的顺序来
```
### 委托构造函数

C++11中出现的新特性，目的是减少构造函数中的代码冗余，委托构造函数可以调用其他构造函数，从而简化构造过程。将构造任务部分或者全部的交给其他构造函数。

默认构造函数还是能有就有，不然在某些情形下缺少默认构造函数会报错，例如初始化vector数组的时候，会调用默认构造函数

### 聚合类与constexpr类

public成员，无成员函数，都是字面值

