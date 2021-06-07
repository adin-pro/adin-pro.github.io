---
title: gdb调试器命令cheatsheet
mathjax: true
date: 2021-02-28 16:16:50
tags:
- c++
categories: cheatsheet
---

gdb使用命令调试

<!-- more -->

以~/Documents/vscTest/main.cpp为例

```cpp

#include <iostream>
 using namespace std;

 int main(){
        int a = 10;
        int b = 10;
        cout<<"b is "<<b <<"\n";
        cout<<"b is "<<b ;
        cout<<"abchd";
        cout<<"b is "<<b <<"\n";
        cout<<"b is "<<b ;
        cout<<"abchd";

 }
     
```

使用cmake生成的可执行文件放在build文件夹下

```bash
adinvm@ubuntu:~/Documents/vscTest/build$ gdb
GNU gdb (Ubuntu 9.2-0ubuntu1~20.04) 9.2
Copyright (C) 2020 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word".
(gdb) file main
Reading symbols from main...
```
使用file [filename] 打开相应文件

```bash
(gdb) b 2
Breakpoint 1 at 0x10e0: file /home/adinvm/Documents/vscTest/main.cpp, line 4.
(gdb) b 7
Breakpoint 2 at 0x1149: file /home/adinvm/Documents/vscTest/main.cpp, line 9.
(gdb) b 9
Note: breakpoint 2 also set at pc 0x1149.
Breakpoint 3 at 0x1149: file /home/adinvm/Documents/vscTest/main.cpp, line 9.
```
使用b [number]  在某一行进行断点的设置

```bash
(gdb) r
Starting program: /home/adinvm/Documents/vscTest/build/main 

Breakpoint 1, main () at /home/adinvm/Documents/vscTest/main.cpp:4
4	 int main(){
(gdb) r
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /home/adinvm/Documents/vscTest/build/main 
```
r命令为restart，启动或者重启

```bash
Breakpoint 1, main () at /home/adinvm/Documents/vscTest/main.cpp:4
4	 int main(){
(gdb) c
Continuing.
b is 10

Breakpoint 2, main () at /home/adinvm/Documents/vscTest/main.cpp:9
9	        cout<<"abchd";
```
c命令为continue，断点单步执行，执行时才有效果

```bash
(gdb) p a
$1 = 10
(gdb) p b
$2 = 10
(gdb) c
Continuing.
b is 10abchdb is 10
b is 10abchd[Inferior 1 (process 31811) exited normally]
(gdb) c
The program is not being run.
(gdb) c
The program is not being run.
```

info b展示出来所有的断点

```bash

(gdb) info b
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   0x00005555555550e0 in main() 
                                                   at /home/adinvm/Documents/vscTest/main.cpp:4
	breakpoint already hit 1 time
2       breakpoint     keep y   0x0000555555555149 in main() 
                                                   at /home/adinvm/Documents/vscTest/main.cpp:9
3       breakpoint     keep y   0x0000555555555149 in main() 
                                                   at /home/adinvm/Documents/vscTest/main.cpp:9

```

