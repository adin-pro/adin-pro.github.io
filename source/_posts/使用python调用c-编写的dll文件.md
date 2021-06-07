---
title: 使用python调用c#编写的dll文件
mathjax: true
date: 2021-02-26 13:24:52
tags:
- python
categories: python
---

遇到了一个第三方的API库，是使用c#写的dll库，为了便于与项目的其他部分融合（也是为了想降低coding难度哈哈），想要使用python操作dll库，做了一番探索，做一个记录。

<!-- more -->

网上大致搜索了一下，python可以通过第三方库的支持来读取调用dll文件，有一个叫ctypes之类的库可以读取c编写的函数，针对c#有一个专门的库叫pythonnet。

使用pip install pythonnet或者上pypi手动下载wheel文件完成安装，需要注意手动安装时要选择适配python3.x版本的相应文件，网上有很多人版本没有选对。

在这里也大致说一下我对c#所写dll文件结构的理解

```c#
classlib.dll

mynamespace{
    myclass{
        func1;
        func2;
    }
}
```
一个dll文件中包含着命名空间，dll文件名不需要和namespace的名字一样（使用vs2017生成dll时默认与namespace名字一致），命名空间mynamespace中含有相关的类和方法。

```c#
using mynamespace;

inst = myclass();
var result = inst.func1();
```
在c#中使用时，声明使用dll文件中的命名空间，就可以使用里面的类和方法，用法是比较简单的。在python中我们也期待可以用相似的方式导入命名空间，直接访问相应的类和方法。

```py
import clr # 是的不叫pythonnet，那个只是包名字
clr.AddReference('classlib') # 注意没有dll后缀
from mynamespace import *

inst = myclass()
res = inst.func1()

```
将dll文件放到与py文件相同的文件夹，使用上述的方法就理论可以完成namespace的导入和使用

**但是！**

使用过程中也有一些奇怪的，我不能理解的问题：
在项目中有个dll文件一直提示找不到这个模块，无论是放在同一个目录下还是将它加入到path系统变量都不行，有一些方案提示使用绝对路径地址，或者使用
```py
clr.FindAssembly("absolute path.dll") # 注意有dll后缀
```
这些方法对于我来说都不是很好用，后来这个dll文件没有实际使用，FindAssembly和AddReference两个函数有什么区别我也没搞清，pythonnet项目的github地址上给的例子使用的是AddReference，我最终使用的也是这个，FindAssembly有啥用怎么用看之后如果有机会碰到了再研究吧

今天是体会到python为什么叫胶水语言了，确实简单好用

下一篇与此项目相关的内容暂定为python调用windows COM组件的方法，和C++调用COM一对比就看出来python是真的简单啊

