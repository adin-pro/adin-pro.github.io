---
title: CppPrimer5th-Chapter5
mathjax: true
date: 2021-01-28 14:06:49
tags:
- C++
- 基础知识
categories: C++
---
《C++ Primer 5th》第五章中的重要知识点，第五章内容为语句

<!-- more -->

## 异常

C++标准库定义了一组类，用于报告标准库函数遇到的问题。这些异常类也可以在用户编写的程序中使用，它们分别定义在4个头文件中：
· exception头文件定义了最通用的异常类exception。它只报告异常的发生，不提供任何额外信息。
· stdexcept头文件定义了几种常用的异常类，详细信息在表5.1中列出。
· new头文件定义了bad_alloc异常类型，这种类型将在12.1.2节（第407页）详细介绍。
· type_info头文件定义了bad_cast异常类型，这种类型将在19.2节（第731页）详细介绍。

<img src='errtype.png' width=600 title='errtype'>

异常类型只定义了一个名为what的成员函数，该函数没有任何参数，**返回值是一个指向C风格字符串**的const char＊。该字符串的目的是提供关于异常的一些文本信息。

<img src='what.png' width=600 title='what'>

