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


---------
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


**破折号被叫做dash，ls -l发音 L-S-dash—L**

## 命令特征

shell分为两大类，Bourn Shell和C-Shell，Bash是Bourn Again Shell（Born-Again），属于前者族系，两者在语法上有所不同

命令 选项 参数

ls **options** *filename...*

1. 方括号里面的是可选择的
2. 不在方括号里的是必须写的
3. 黑体字必须原样输入
4. 斜体字使用适当内容替换
5. 接省略号...代表可以重复任意多次
6. 如果一个单独的选项和参数放在一起，则必须同时使用或者不用
7. 竖线分开的多个项目代表选择

- echo $SHELL 查看当前shell
- less /etc/shells 查看系统上装的所有shell
- 每次执行shell脚本的时候，实际上是启动了一个新的shell

### 环境、进程和变量

- 环境：一组用来存放信息的变量
- 变量：一个用来存储数据的**实体**，不能数字打头，分为全局和局部变量，默认为局部shell变量，使用export可以导出到环境中,unset用于复位（清除）变量
- 进程：父子进程之间有全局变量的作用

env和printenv命令可以显示环境变量，set显示shell变量与它们的值

**元字符**：类似<>这种具有特殊含义的标点字符称为元字符，元字符在双引号中会解释为普通字符

```bash
adin@LAPTOP-VA1IGCFC:~$ echo I am $USER
I am adin
adin@LAPTOP-VA1IGCFC:~$ echo I am <$USER>
-bash: syntax error near unexpected token `newline'
adin@LAPTOP-VA1IGCFC:~$ echo "I am <$USER>"
I am <adin>
```

set -o | +o *switchname*这种可以改变shell本身的设置，比如ignoreeof，一般用户比较少修改，默认配置在bashrc文件中

--------------

## 使用shell：命令和定制

### 元字符

各种各样的字母数字字符alphanumeric character，包括空格，制表符，回车等等。其中重要的一类称作通配符globbing，用于文件名的扩展。

在引号之内就“引用”了元字符，会取消转义。反斜杠\也是重要的转义字符，使用双引号的时候会保留 $ ' \这三个的特殊含义。而单引号会把所有元字符的意义覆盖，变成纯粹的文本。也因此，单引号称作**强引用**，双引号对应称作**弱引用**。

### shell提示

修改环境变量PS1可以改变提示行的格式，编写该格式的时候可以使用*特殊码*，例如\u代表$USER，\h代表hostname

### 历史

fc -l可以展示历史命令，！num可以执行对应事件的编号

fc -s p=q可以让q命令以p命令的参数重新执行一遍

^R可以让用户输入一个模式，然后history去寻找与之匹配的格式

### 别名
alias 可以起别名；
unalias取消别名

## 使用shell：初始化文件

两个特殊文件也就是初始化文件：

- 登陆文件login file：每次登录时自动执行的命令 .profile
- 环境文件environment file：在新shell启动时自动执行的命令 .bashrc(bash run commands)
- 这个其实也算第三个特殊文件，登出文件logout file，可以在注销的时候执行一些命令 .profile_logout

- 交互式shell：输入命令行时，又分为登录shell和非登录shell
- 非交互式shell：执行shell脚本时

**打开一个终端不需要登录，属于非登录shell，**打开虚拟控制台则需要登录。打开**ssh需要登录**，在shell打开一个新sh不需要登录。

登录shell执行登录文件和环境文件，非登录shell只执行环境文件。但是bash默认模式只执行登陆文件，而不执行环境文件？？？？

## 管线命令与标准IO

### 标准IO

- 标准输入 0< <<
- 标准输出 1> >>
- 标准错误 2> >>
控制 noclobber的选项开关，*可以控制是否对已有文件进行修改*
- ‘>| 强制重写’
- 2&>1 将标准错误定向到标准输出

### 管线命令
tee是分流命令
```bash
cat names1 names2 names3 | tee d1 d2 # 把管线输入赋给d1 d2两个文件
who | tee -a d1 # 追加模式
```
以上所有命令都会在标准输出（屏幕）上额外显示一份

## 过滤器

> 过滤器就是能够从标准输入读取文本数据并向标准输出写入文本数据（每次一行）的程序。通常，大多数过滤器都被设计成工具，出色地完成一件事情。

- cat就是把输入变成输出
  - cat > data 把键盘输入内容输出到data
  - cat < data 显示文件内容
  - cat后面可以跟多个文件，依次读入并输出，起到catenate的作用
  - **Warning**cat输入和输出文件如果一样，会将文件内容清空报错
- split划分文件，有点像cat逆操作
  - 默认创建1000行一个的文件
  - split -l n file，以n行每个创建切分文件，默认命名为xaa，xab等
- tac与cat的碰瓷，输出前将文本反转
  - cat -n data | tac | tac两极反转就整回来啦
- rev又一个反转的命令，但是不想tac逐行反转，rev是逐字符反转
- head/tail 平平无奇的显示头尾的命令
- colrm col1 col2 < file 删除指定行

### 比较和抽取

- cmp file1 file2
- comm file1 file2 需要排序文件
- diff file1 file2 表示如何操作可以把文件1变成文件2
  - 文件1使用< 文件2使用 >
  - c改变,d删除,a添加
  - 相当多的选项可以展示两个文件的区别
- sdiff,并排展示,把两个文件都列出来

#### 差分和补丁

```bash
diff foo-2.0.c foo-2.1.c > foo-diff-2.1
```
输出文件包含一列指示,可以将2.0版本更新到2.1版本,这种方式叫做**差分**
使用patch命令可以完成补丁的修补

实际中应用版本控制系统VCS, 不需要自己控制项目的差分补丁

- cut指令作用与colrm相反,它会将指定的列从文件中抽取并且删除掉其他内容
```bash
cut -c 1-8, 5-16 info
```
这种破折号表示范围也是左闭右开的

- paste合并数据列,和cat合并的能力有点类似, 这个可以水平合并, cat是竖直方向合并

### 统计与格式化

- nl提供在文本中插入行号,并输出
- wc word count统计字符数量,输出顺序: 行--line 单词--word 字符--character,可以输入多份文件,多份文件会计算各个项目的总数
- expand将制表符变成空格, vim中 :set list可以可视化展示tab和space
- unexpand将空格变成制表符
- fold可以折叠文本,保证每行字符数量不超过指定值
- fmt格式化段落,让段落文字每句话之间的空格变得规范(例如法国式间距里每个句子后面空两个空格)
- pr为了给要打印的东西编号,按页格式化文本/按列格式化文本,是为了当年程序员们打印纸带的时候排版用的

### 选取排序组合以及变换

- grep最牛的过滤器
搜索包含特定pattern的行, 中间的re代表regular expression
**G**lobal search the **R**egular **E**xpression and **P**rint the line
```bash
grep [-cilLnsvwx] pattern [file...]
```
*讨论若干个重要的命令参数*
```bash
ls -F| grep -c '/' #统计目录的个数
grep -i pattern file #忽略大小写区别
grep -in pattern file #忽略大小写,注明结果所在行ignore
grep -l pattern file1 file2 ... #只显示包含模式的文件名称,适用于多个文件寻找
grep -w pattern file #必须完全匹配
grep -v pattern file #显示不包含模式的行!!!!
grep -r pattern dir #在整个目录里面查找
```

- fgrep是快速grep(已废弃deprecated,现在grep也很快),egrep是扩展grep(使用扩展正则表达式)
- look没有看懂,功能比grep简单很多,不能读取标准输入,但是快,适合优化的时候使用

#### 排序

- sort排序数据或者查看数据是否有序
- sort -o names > names 可以实现对于源文件的排序,不至于出现cat names > names清空自己这种情况
- sort通常就用做管线命令的一部分排个序
- sort -c查看是否有序
- sort -r反序
- sort -u唯一unique
- sort -n排序数字

- uniq与重复行相关的操作

- join会把两个**已排序文件**中键值相同的文本合并