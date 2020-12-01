---
title: Embedded Linux System
mathjax: true
date: 2020-11-28 11:56:41
tags:
- UNIX/LINUX
- 嵌入式
categories: UNIX/LINUX
---

嵌入式Linux系统的一些概念，做一些记录，内容来源于Linux Foundation Instructor John Bonesio的系列文章。
https://thenewstack.io/an-introduction-to-using-linux-in-embedded-systems/

<!-- more -->

## Major components for embedded linux system

介绍嵌入式Linux系统中的相关组件，这里面bootloader常常不算做Linux系统的一部分，而被视作常规嵌入式系统的内容

### bootloader
boot来自于一个笑话
> pick yourself up by your bootstraps

**bootloader: find the Linux kernal's program code, and load it into the memory**

the bootloader is different from what in desktop or server computer.
- desktop: BIOS --> grub(bootloader) --> os
- embedded: init --> Das-uboot(bootloader) --> os

Question: why we need bootloader? why can't the os loaded itself into the memeory
Answer: for flexibility, without bootloader user can not set up multi-os computer

另一种回答说，这个问题提问的答案就是bootloader的作用，上面的回答可能解答了为什么os不放在固件ROM上（不灵活，不可更换，地方不够）

### kernal

**kernal: start the program and provide coordination among them**

the very first program: *init*

init提供系统运行所必须的服务

这一部分kernal和x86上的在运行作用上没什么区别，但底层的实现明显不同

### root file system

storage medium can be SD cards, flash memory or even RAM in embedded system
在init程序运行之前，/根目录就应该挂载好，以放内核找不到init程序而崩溃
在嵌入式系统上的Linux文件系统很不一样

### services

> Linux folks traditionally call these services daemons or daemon programs

通过init程序启动的，在后台运行的服务/守护进程

### summary

when an embedded computer starts, the Linux system will perform these steps:

- jump into the bootloader
- jump into the kernel
- mount the root filesystem
- load and run init
- load and run background services (or daemons)
- load and run applications

### cross compiler

交叉编译：指的是程序编译平台和运行平台不同

