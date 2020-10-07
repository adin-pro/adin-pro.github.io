---
title: UNIX/LINUX大学指南
mathjax: true
date: 2020-09-26 10:52:48
tags:
- UNIX/LINUX
- 基础知识
categories: UNIX/LINUX
---

《Harley Hanh's Guide to UNIX&LINUX》是一本给UNIX/LINUX初学者学习的读物，内容比较活泼有趣，这里记录一些有趣的知识点。

<!-- more -->

## 什么是UNIX和LINUX

UNIX是贝尔实验室的产品，版权在AT&T手里，但是因为版权问题一直受到广大程序员的不满，不断尝试突破原本System的限制，BSD和Linux都可以说是这种开放精神的产物。

UNIX是一种多用户、多任务处理的操作系统。包括一个类UNIX内核，许多类UNIX实用工具和一个类UNIX shell组成。

Linux是任何使用Linux内核的Unix的名称。（遵循GNU的GPL协议）

从我理解的角度，目前的UNIX=BSD发行版+LINUX发行版+SystemV发行版（存疑）。

>  类UNIX这个术语很怪异也解释不清楚是什么东西，但是大家看到了都明白在说什么哈哈哈

## UNIX工作环境
### 多任务处理

多任务处理实际上是快速执行多个不同的任务，使之看起来是在多任务处理。


## 命令行

### 登录与基本用户相关

- 查看日期：date，cal,calendar
- 与登录状态相关：who，w，whoami，passwd，last
- 系统状态：hostname，uname，uptime
- 注销：logoout，exit，login（不一定完全注销），^D（强行注销）
- echo $TERM 查看终端类型，stty显示或改变终端设置，tty查看终端内部名称
  - tty是teletype终端输入（其实就是键盘）的缩写
  - print也是因为之前的终端输出是打印纸带而命名的

### Unix键盘信号

>键盘映射系统可以适用于任何类型键盘，这种映射可以被改变，理解并改变是Unix CLI（Command Line Interface）的使用者必备技能。

stty -a可以显示目前的映射（set terminal -all）

#### 键入过程中的信号

- erase删除最后一个字符
- werase删除最后一个键入的单词(maybe word-erase?)
- kill删除整行

*Backspace* 或者 *Delete* *^H* 负责发送erase信号

*^W*会触发werase信号

*^X*或者*^U*触发清除整行的kill信号，在WSL中测试为*^U*

>kill信号并不会停止程序，它只删除刚键入的一行，为了停止需要^C中断信号

#### Backspace从何处来

以前的打印机终端的输入输出都是打印纸带孔，每一个孔代表2进制的一位0/1，如果输入错误了，不能像现在的屏幕直接删掉，而是要先回位（输入^H，回位Backspace，BS命令），然后*Rubout*（擦掉，发送DEL命令），使得穿孔机8位都打孔置1，使得该忽略该字符。（127无效？）

^H后来由Backspace按键代替，Rubout由Delete代替

*后来混乱了，Backspace直接映射到了erase，乱七八糟的我也看不懂。现在PC上的delete和当时的delete也完全不是一个东西了，没有映射到erase。*

**按下^/在我的WSL中是撤销操作**

#### 神秘字符^H

>如果发现自己狂按backspace没有出现删除效果，而是出来了好几个^H，代表在你的远程主机那里，^?被映射位erase信号，你发送的都是无效的指令。

为了测试这个东西，我用了书上的stty erase ^H命令，将主机的映射改变了，结果发现WSL的终端上backspace映射的是^?，导致我现在按下backspace会出来一堆^?，着实可爱

```bash
adin@LAPTOP-VA1IGCFC:~$ stty erase ^H
adin@LAPTOP-VA1IGCFC:~$ ^?^?^?^?^?^? 
```

为了解决这个问题，我还是改回去了 stty erase ^?

```bash
adin@LAPTOP-VA1IGCFC:~$ stty erase ^?
```

#### 停止与启动程序

- intr：

一些程序为了防止误操作，把^C命令给禁止了，比如vi，所以在编辑的时候经常会出现奇奇怪怪的神秘映射后字符。

- quit：

quit是当年用于调试用的老命令，停止程序加复制一份内存保留，以便后续分析当时的原因，文件命名为core（内存的老名字），称作**磁芯文件**

命令是^\，但在WSL上测试发现什么都没有发生（可能是时机不对，在bash没有运行的时候单独按没反应）

- stop：

^S发送stop信号用于终止屏幕的显示，在大篇幅疯狂滚动的时候会有用，当然更好的是less命令

- start

^Q发送start命令，和之前的stop对应着，恢复屏幕的输出，如果屏幕


#### 终止信号eof

这个比较熟悉了^D发送输入终止信号，end of file，同时也是注销shell的命令

可以使用环境变量{IGNOREEOF}来封闭eof信号，数字代表连续按下多少次才能注销


#### 返回与换行

也是为了适配以前打印机打印纸换行问题的信号，对应键盘上的^M（返回）和^J（换行），这两个都可以代替回车


### 立即使用程序吧

#### 查找which type whence

which和type适用bash

whence适用korn shell

#### 一些辅助功能
- lock：锁定终端15min，或者自己指定时间，只能用当前user或者root的密码才能解锁
- leave：提醒结束工作，设定离开时间，超过时间后每分钟催促你一次
- bc：计算器bc -l启动库函数，scale小数，ibase/obase输入输出基

#### 说明书和bang

**RTFM：Read The Fucking Manual**

man开启说明书，/pattern搜索

！命令称作bang字符命令，可以暂停当前程序向shell发送一条命令，在读manual的时候可以去做别的事情

man其实是一个包含9个章节的手册，默认输入命令会打开它们在第一章节里的内容，如果需要看其他部分，可以在 man命令后面跟上数字

```bash
adin@LAPTOP-VA1IGCFC:~$ man 2 which
No manual entry for which in section 2
See 'man 7 undocumented' for help when manual pages are not available.
adin@LAPTOP-VA1IGCFC:~$ man 2 kill
```
上述打开了kill在第二章节系统调用中的内容，which命令没出现在第二章节所以打不开


