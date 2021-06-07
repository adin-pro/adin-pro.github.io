---
title: CppPrimer5th-Chapter3
mathjax: true
date: 2021-01-27 16:44:35
tags:
- C++
- 基础知识
categories: C++
---
《C++ Primer 5th》第三章中的重要知识点

<!-- more -->

阅读《C++ Primer 5th》后记录下比较重要或者不熟悉的知识点，方便以后查询复习。本篇为第三章字符串、向量和数组，讲述了string、vector与迭代器的一些知识。

## using命名空间的声明
不同于常用的 *using namespace std* ，这里primer推荐的是针对每一个要用的函数单独声明，这样不会让命名空间保护的名称全部暴露出来。
单独声明某一个函数的格式为 *using namespace::func*
```cpp
using std::cout; using std::cin; using std::endl;
using std::string;
```
## 标准库string

### 拷贝初始化与直接初始化
>C++语言有几种不同的初始化方式，通过string我们可以清楚地看到在这些初始化方式之间到底有什么区别和联系。如果使用等号（=）初始化一个变量，实际上执行的是拷贝初始化（copy initialization），编译器把等号右侧的初始值拷贝到新创建的对象中去。与之相反，如果不使用等号，则执行的是直接初始化（direct initialization）。

```cpp
string s1 = "abcd"; // copy
string s2("hiya"); //direct
string s3(10, 'c'); //direct "cccccccccc"
```
### 使用getline读取一整行
一般使用cin读取字符串流的时候，会把空格、制表符、换行符当作分隔符。如果想要保留这些分隔符（除了换行符），可以使用*getline()*

>getline函数的参数是一个输入流和一个string对象，函数从给定的输入流中读入内容，直到遇到换行符为止（注意换行符也被读进来了），然后把所读的内容存入到那个string对象中去（注意不存换行符）。getline只要一遇到换行符就结束读取操作并返回结果，哪怕输入的一开始就是换行符也是如此。如果输入真的一开始就是换行符，那么所得的结果是个空string。

*getline()* 同样返回流对象，因此可以用while来判断是否读到底部了

```cpp
int main(){
string line;
while(getline(cin, line)){
    cout << line << endl;
    }
return 0;
}
```

### size_type
>string类及其他大多数标准库类型都定义了几种配套的类型。这些配套类型体现了标准库类型与机器无关的特性，类型size_type即是其中的一种。在具体使用的时候，通过作用域操作符来表明名字size_type是在类string中定义的

size_type是一个无符号量，使用的时候尽量不要与整型（有符号量）混用，防止出现意外得到负数的情况，进而致使 *size()+10 < n* 这种条件无法正常判断。

### 字符串字面值

为了与c兼容和历史原因，字符串字面值类型不是string类，无法执行相关的操作。

### 处理字符串中的字符
一般来说，C++程序应该使用名为cname的头文件而不使用name.h的形式，标准库中的名字总能在命名空间std中找到。如果使用.h形式的头文件，程序员就不得不时刻牢记哪些是从C语言那儿继承过来的，哪些又是C++语言所独有的。

例如 *cctype* 和 *ctype.h* 文件实际上是同一个文件（从包含的函数功能来说）， *cctype* 符合c++命名规范

处理字符串中每个字符元素最好的方式是使用c++11的range for

```cpp
for(declaration : expression)
    statement

string str("some string");
decltype(str.size()) cnt;
for (auto c : str){
    if(isdigit(c))
        ++cnt;
    cout << c << endl;
}
cout << cnt << "digits in str\n";
```

**如果修改字符，需要将auto后面增加引用修饰符 ，即 auto &c : str**

## 标准库中vector
>vector是模板而非类型，由vector生成的类型必须包含vector中元素的类型，例如vector<int>。

vector能容纳绝大多数类型的**对象**作为其元素，但是因为**引用不是对象**，*所以不存在包含引用的vector*。除此之外，其他大多数（非引用）内置类型和类类型都可以构成vector对象，甚至组成vector的元素也可以是vector。

### 不同括号初始化效果不同

```cpp
vector<int> v1(10,1); //十个元素都是1
vector<int> v2{10,1}; //前两个元素是10和1
```
**一些重要的注意事项**

>开始的时候创建空的vector对象，在运行时再动态添加元素，这一做法与C语言及其他大多数语言中内置数组类型的用法不同。特别是如果用惯了C或者Java，可以预计在创建vector对象时顺便指定其容量是最好的。然而事实上，通常的情况是恰恰相反。

>随着对vector的更多使用，我们还会逐渐了解到其他一些隐含的要求，其中一条是现在就要指出的：如果循环体内部包含有向vector对象添加元素的语句，则不能使用**范围for**循环

### vector的size_type

vector的size_type需要指明具体模板元素
```cpp
vector<int>::size_type  //right
vector::size_type //wrong!
```

>两个vector对象相等当且仅当它们所含的元素个数相同，而且对应位置的元素值也相同。关系运算符依照字典顺序进行比较：如果两个vector对象的容量不同，但是在相同位置上的元素值都一样，则元素较少的vector对象小于元素较多的vector对象；若元素的值有区别，则vector对象的大小关系由第一对相异的元素值的大小关系决定。

只有元素支持比较的时候，vector容器才可以比较

## 迭代器

如果容器为空，begin和end是同一个迭代器
```cpp
auto be = v.begin(), ed = v.end();
auto cbe = v.cbegin(), ced = v.cend();
```
一般来说不在意也不清楚迭代器的具体类型，使用auto关键字来代替准确的类型
cbegin和cend是专用得到const_iterator类型的返回值

**两个迭代器相加没有意义，相减能够得到之间的间距，类型为__int64**

## 数组

数组在c++11中利用begin end函数可以近似得到头指针和尾指针（最后元素的后一个内存位置）
```cpp
int a[]={1,2,3,4};
auto be = begin(a);
auto ed = end(a);
cout << *(ed-1)<<endl;
```
>两个指针相减的结果的类型是一种名为ptrdiff_t的标准库类型，和size_t一样，ptrdiff_t也是一种定义在cstddef头文件中的机器相关的类型。因为差值可能为负值，所以ptrdiff_t是一种带符号类型。

为了兼容c风格字符串，string类型做出了兼容性配置
- 允许使用以空字符结束的字符数组来初始化string对象或为string对象赋值
- 在string对象的加法运算中允许使用以空字符结束的字符数组作为其中一个运算对象（不能两个运算对象都是）；在string对象的复合赋值运算中允许使用以空字符结束的字符数组作为右侧的运算对象

反过来，如果以string作为右值，不可以直接赋给c风格字符串
- string类具有c_str函数，可以返回一个c风格字符串
```cpp
const char c[] = str; // wrong!
const char c[] = str.c_str(); //right
```

## 多维数组

多维数组的初始化可以用花括号分隔每一行的数据

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <cctype>
#include <cstring>

using std::vector;
using std::begin;
using std::end;
struct foo {};
using std::string; using std::cout; using std::cin; using std::endl;

int main() {
	int ia[3][4] = {
		{1,2,3,4},
		{5,6,7,8},
		{9,10,11,12}
	};

	for (int (*p)[4] = ia; p!=ia+3; p++)
		for (int *m = *p; m!=*p+4; m++)
			cout << *m << endl;
	return 0;
}

/************************************/

int (*p)[4] = ia; //right! 指向4元素数组的指针
int *p[4] = ia; //wrong! 整形指针的数组

用auto解决这个问题最快乐

```

我们首先明确（＊p）意味着p是一个指针。接着观察右边发现，指针p所指的是一个维度为4的数组；再观察左边知道，数组中的元素是整数。因此，p就是指向含有4个整数的数组的指针。

