---
title: C++草稿本
mathjax: true
date: 2020-09-23 09:22:27
tags:
- C++
- 基础知识
categories: 基础知识
---

记录C++语法中的一些小问题


<!-- more -->


1. 初始化方式包括用=赋值，还有使用括号。例如 int a(1)。使用括号更加适配c++对象。
2. 为什么switch语句会使用“不加break就能一直运行所有语句”这种机制？某些情况下可以对此加以利用，可以用于检测原因字母是否存在，替代一大堆或结构。
```cpp

switch(nextchar) {
    case 'a': case 'A':
    case 'e': case 'E':
    case 'i': case 'I':
    case 'o': case 'O':
    case 'u': case 'U':
        ++vovel_cnt;
        break;
}
```
3. cin的返回值不是一个内嵌类型，而是cin对象。对于while(cin>>nextchar)这种结构，结束循环的条件是EOF。在windows平台输入EOF的方法为，同时按下ctrl和z键。 如果是unix/linux平台，则需要同时按下ctrl+d键。
4. array数组的初始化数量必须用常量定义而非变量。即int s[variable]这种操作是非法的。可以留空，由编译器仔细根据初值数量判断。**但是没有初始化数值又不明确数组长度的操作是非法的，编译器无法确定数组长度**
```cpp
int a =10;
int s[a]; // Error!

```
5. array如果定义后不初始化，则数组元素为随机值。如果初始化={}，则所有元素被初始化为0，如果初始化={1}，则第一个元素为1，其余元素为0。
6. vector不支持={}这种初始化方式，只能逐个赋值或者用数组给他初始化，没有被初始化的元素默认值为0。
```cpp

vector<int> ini(5) = {1,2,3,4,5}; //ERROR!!!!

ini[0] = 1; //OK

```
7. vector和string都是std命名空间里的，使用时注意。
8. 指针初始化指向地址0，则等同于null指针，即什么对象都没有指向。int* p=0;
```cpp

define NULL 0
完全等价===

```
9.  srand()和rand()属于cstdlib库，使用时要配合time.h，使用time(NULL)或者time(0)生成srand()的种子，才能生成随机序列。
```cpp
srand(time(NULL))
rand() // OK

```
10. int[]这种写法只能在数组聚合初始化的时候使用，其余时候不等于int*。
11. 单引号表示一个字符'a'双引号代表字符串"a" = 'a'+'\0'。类型为char和const char。**两个字符类型的变量没办法直接拼接，必须至少有一个是string**
12. ofstream和ifstream对象在未打开对象的时候会返回false。
13. ifstream读取文件的时候，空格和tab都可以作为分隔符，ifstream会依次读取，逗号尝试了不能作为分隔。
14. fstream可以满足io的读取功能，打开模式如果是ios_base::in | ios_base::app 则一开始的文件位置光标会位于文件末尾。需要使用fstream.seekg(0)重新定位光标位置到文件开头。
15. 函数参数用指针和引用都可以作为输出值，使用指针时需要检查是否为空指针，而引用要求必须是存在的对象不需进行这个检查，也就说只要编译顺利通过，引用对象是不会发生runtime error的。一般的对象引用使用const修饰，除非要修改变量内容，**一般内置变量不建议使用传址机制**。
16. 如果一个变量定义为了**全局变量（file scope）**，而又是**内置类型**，则它**会被自动初始化为0**（即使没有初始化语句）。而一个local scope里的变量除非指定初值，否则不会被初始化。
17. 函数的默认参数值必须放在不带默认值的参数的后面，这一点和python一致。
18. 在头文件声明的函数形式具有更高的可见性visibility（如头文件声明了而cpp文件没有声明默认值，以头文件为准）
19. inline内联函数一般要在头文件里面声明。
20. 返回值不同不能让函数重载，因为在调用的时候编译器无法用返回值判断是哪个函数。
21. function template可以重载
```cpp

template<typename T> void display(const string &msg, vector<T> vecT);

template<typename T> void display(const string &msg, list<T> vecList);

```
22. 函数返回值为 const  只有用在函数返回为引用的情况。 函数返回值引用常量表示不能将函数调用表达式作为左值使用。
```cpp

int& mini(int &a,int &b);
    |
    v
mini(a,b) = 4; // OK, func returns a lvalue

----------------
const int& mini(int &a, int&b);
    |
    v
mini(a,b) = 4; //ERROR! function mini returns a rvalue
```

23. c风格字符串是字符数组，char str[]跟一般数组一样，方括号里如果不写长度，那么就必须在后面直接用字符串赋值初始化。
```cpp
char str[]="hello"; // must be initialized
char str[]={"myworld"}; // must be initialized
char str[20] = "abcd"; // initilaztion is not mandatary

```

24. strlen()是一个函数，sizeof是一个运算符
    1.  sizeof 操作符的结果类型是 size_t，它在头文件中 typedef 为 unsigned int 类型。该类型保证能容纳实现所建立的最大对象的字节大小。
    2.  sizeof 可以用类型做参数，strlen 只能用 char* 做参数，且必须是以 \0 结尾的
    3.  数组做 sizeof 的参数不退化，传递给 strlen 就退化为指针了
    4.  大部分编译程序在编译的时候就把 sizeof 计算过了，是类型或是变量的长度，这就是 sizeof(x) 可以用来定义数组维数的原因
        ```cpp
        char str[20]="0123456789";
        int a=strlen(str); // a=10;
        int b=sizeof(str); // 而 b=20;
        ```
    5.  strlen 的结果要在运行的时候才能计算出来，是用来计算字符串的长度，不是类型占内存的大小
    6.  sizeof 后如果是类型必须加括弧，如果是变量名可以不加括弧。这是因为 sizeof 是个操作符不是个函数
```cpp
char str[20]="0123456789";
int a=strlen(str);         // a=10; >>>> strlen 计算字符串的长度，以结束符 0x00 为字符串结束。
int b=sizeof(str);         // 而 b=20; >>>> sizeof 计算的则是分配的数组 str[20] 所占的内存空间的大小，不受里面存储的内容改变。
```

25. char[]和“char*”这个东西，在某些时候是可以转换的，比如printf希望获得一个“char*”的输入，这时候使用字符数组就会自动退化为字符指针。**vs2019**里面用const char* *给* char*赋值是forbidden的，例如:
```cpp
char* p = "world"; // Error 
char* p = (char*)"world"; //OK

在c语言里用指针“初始化赋值字符串”实际上指向的是一段静态字符串地址（Readonly），不可修改。而用字符数组是copy了一份，与原有的静态字符串无关了，可以修改。

char s[] = "hello" ;
then s[0] = 'j'; //legal
char *s = "hello" ; 
then *s = "world"; //illegal!!

```
26.  char** 是指针的指针，与char*[]代表的有所区别
    > A "double star" is a pointer to a pointer.
```cpp
char a[]= "abcdefg";
char* p = a; 
char** pp = &p;
cout<<*(*pp+1);

输出结果为b
```


27. >There is one difference between an array name and a pointer that must be kept in mind. A pointer is a variable, so pa=a and pa++ are legal. But an array name is not a variable; constructions like a=pa and a++ areillegal.
——The C Programming Language by Brain W. Kernighan & Dennis M. Ritchie
```cpp
	char s[10] = "Hello";
	char b[15] = "loo";
	char c[20];
	char* m;
	m = c;//OK
	m = s;//OK
    c = b;//ERROR!!!
    c = s;//ERROR!!!

```

28.  const int* p1 vs. int* const p2
```cpp
    const int a = 10;
    const int* p1 = &a;  // initialization is not mandatory 
    *p1 = 5; // wrong
    p1++;    // right  

-----------
	int a = 10;
	int* const p2=&a; // must be initialized when declare
	*p2 = 5;  //right
    p2++; // Wrong

```
29. [链接：数组名称是指针吗？](https://stackoverflow.com/questions/1641957/is-an-array-name-a-pointer)不是的，但数组名称经常退化为指针

30. cin.fail()检查cin的非法输入。
```cpp
	int a;
	cin >> a;
	if (cin.fail()) cout << "invalid input";
```
31. function pointer有点像泛化的函数，是一种更广泛的泛化
```cpp
用ptr指代所有参数、返回值符合要求的函数，ptr默认值0检验是否真的调用了函数
void (*ptr)(int, int)=0;

void dis(int a, int b) {
	cout << a << '\n' << b;
};

void bigdis(int c, int d,
	void (*ptr)(int, int)) {
	if (!ptr) {
		cout << "error"; return;
	}
	ptr(c, d);
}
int main()
{
	int a = 10;
	int b = 99;
	bigdis(a, b, dis);
}

```
32. 函数指针还能是数组形式的！
```cpp
需要注意函数指针数组的声明需要放在函数定义之后，或者函数提前声明，否则也会有函数名未定义的问题

void dis(int a, int b) {
	cout << "dis0"; cout << a << '\n' << b;
};

void dis1(int a, int b) {
	cout << a << '\n' << b;
	cout << "dis1";
};

void dis2(int a, int b) {
	cout << a << '\n' << b;
	cout << "dis2";
};

void (*ptr[])(int, int) = { dis, dis1, dis2 };

void bigdis(int c, int d,
	void (*ptr)(int, int)) {
	if (!ptr) {
		cout << "error"; return;
	}
	ptr(c, d);
}
int main()
{
	int a = 10;
	int b = 99;
	bigdis(a, b,ptr[1]);
}
```

33. vector使用vector.at(i)函数可以访问第i个位置的元素，在使用vector指针的时候可以这么用，或者（*P）[i]也可以。vector.front()与vector.back()分别用于提取首尾元素。

34. 引入头文件的时候如果用双引号那么会优先在项目目录下面找，默认是用户提供的头文件。而如果使用<>那么会认为是系统提供的头文件，会先去默认的磁盘目录找。

35. iterator也叫泛型指针，是对指针的一个抽象化和包装
36. 每一个容器类型（含string）的共同操作
    1.  ==equality和！=inequality运算符
    2.  =赋值assignment
    3.  empty(), true/false
    4.  size(), num of elems
    5.  clear(), clear all
    6.  begin(), end(), return the iterator, the first element and the **(last+1) **element
    7.  insert(), erase(), insert and remove
37. 使用数组为vector初始化时，数组提供两个iterator或者说哨兵，要符合first和last的习惯，即要用数组元素第一个指针，和数组最后一个元素的再后一个指针来赋值。
```cpp
	int a[] = { 2,3,4,5,6,7,8,9,20 };
	vector<int> veci(a, a + 9);
	cout << veci.back()<<endl; // output is 20
```

38. 函数对象是一个实现了调用操作符（）的对象，c++里有许多内置的函数对象，它可以替换函数指针，从而将调用某个函数的过程简化为内联，提高调用效率。某些STL算法函数只允许输入一个函数指针，而一个函数指针也只能有一个参数，一旦需求改变，还需要重新编写函数。函数对象可以利用重载调用操作符实现前述函数指针的功能，同时还可以利用对象实例化的参数改变函数的功能。

39. function object adapter可以实现“参数绑定”的功能，将某一个值绑定到函数对象的参数，感觉是使之固定的样子。
```cpp
题目：依据字符串长度排序，使用函数对象作为泛型算法**sort**的参数
class LessThan {
public:
	bool operator()(const string s1, const string s2) {
		return s1.size() < s2.size();
	}
};
// 关键在于sort那一句，前两个参数是要排序的迭代器，最后一个是定义的函数对象
int main() {
	ifstream infile("practice.txt", ios_base::in);
	vector<string> vecs;
	string ex[] = { "a","an", "or","but","the" };
	set<string> exset(ex, ex + 5);
	string tword;
	while (infile >> tword) {
		if (exset.count(tword)) continue;
		vecs.push_back(tword);
	}
	sort(vecs.begin(), vecs.end(),LessThan());
	vector<string>::const_iterator it = vecs.begin();
	for (; it != vecs.end(); it++)
		cout  << *it<< endl;
}

```


40. 使用map来统计文章词频，使用索引方式首次加入的key会被设为默认值value
```cpp
int main() {

	map<string, int>words;
	words["vermeer"] = 1;
	string tword;
	while (cin >> tword) {
		words[tword]++;
	}
}
```

41. 查找map里是否存在某个key可以使用三种方法
```cpp
No.1
int count = words["key"]; // 如果不存在key，会导致words额外增加了一个条目pair class
No.2
map::iterator it = words.find("key"); // find返回一个iterator
No.3
int count = words.count("key") ? words["key"] : 0 ; // 先看看是否存在
```
42. $\color{red}{Iterator Inserter以及iostream Iterator没看懂}$

43. vector.emplace_back()代替vector.push_back()

44. 如果想读取某一个文本文件某一行的第几个，可以用getline函数与string类的find_first_of()函数配合，进行字符串的切割。用string.substr()提取子字符串。

45. 实例化对象的时候，如果想用默认的构造函数不能带括号，因为cpp为了兼容c，默认带括号的都是调用函数
```cpp
SomeClass obj();//ERROR!
SomeClass obj; //Default Constructor
```
46. copy constructor()复制构造函数的目的，需要申请堆空间的变量不能直接复制给别的对象，会出现野指针的问题。为某个class编写copy constructor了之后还有必要编写copy assignment operator。

47. const & mutable
```cpp
class Triangular{
	public:
		// const member function.
		// writing is forbidden
		int length() const {return _length;}
		int beg_pos() const {return _beg_pos;}
		int elem(int pos) const;
		// non-const member function
		bool next(int &val);
		void next_reset() { _next = _beg_pos-1;}
	private:
		int _length;
		int _beg_pos;
		mutable int _next;
	
		//static data member
		static vector<int> _elems;
}; 

// function defined outside the class must declare with keyword "const"
int Triangular:: elem(int pos) const{
	return _elems[pos-1];
}

bool Triangular::next(int &value) const {
	if ( _next < _beg_pos + _length -1 ){
		value = elems[_next++];
		return true;
	}
	return false;
}

```
如果一个class里面有同名函数，但一个是const，一个是non-const，那么在调用函数的时候，编译器会自动为调用者选择合适的被调用函数！

mutable是一种变量声明，它表示mutable变量尽管有所改变，但这种改变不影响对量的常量性，例如next函数只是移动了指针指向数组元素，本质上只是一种迭代器，对数组本身没有根本性的影响。这种变量应当允许被const函数改变。

48. this 是个指针
```cpp
this->member
*this // object
```

49. 运算符重载中，递增的前置版本参数列表为空，后置版本本来也是空的，但是为了重载，后置版本强行给加了一个int参数。
```cpp
inline Triangular_iterator& Triangular_iterator::operator++(){
	++_index;
	check_intergrity();
	return *this;
}

// 这应该是第二次看到函数参数只是一个类型名称，第一次看到是在函数指针
inline Triangular_iterator Triangualr_iterator::operator++(int){
	Triangular_iterator tmp = *this;
	++_index;
	check_intergrity();
	return tmp;
}

```
50. 友元函数声明需要保证被声明的函数的定义在此声明之前，或者只能用友元类做整体的声明。

51. maximal munch规则，合法符号序列总是以最长的那个解释，例如
```cpp
vector<vector<int> >后面两个尖括号必须分开，如何中间没有空格就会被解释为>>流符号
a+++b会解释为a++ + b
```

52. 面向对象风格：继承inheritance、多态polymorphism、动态绑定dynamic binding

53. protected为只有子类可以访问，子类使用时不需要区分“继承而来的成员”还是“自身定义的成员”，在派生类的构造函数中，如果基类需要构造参数，需要先完成基类的初始化。

