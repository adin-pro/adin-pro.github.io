---
title: 使用paramiko远程登陆虚拟机执行命令
mathjax: true
date: 2021-02-22 20:44:01
tags:
- python
categories: python
---

在win10环境下撰写python脚本，ssh登录虚拟机，并执行命令和返回结果

<!-- more -->

今天接到了一个小任务，需要在windows环境下写个python脚本执行远程机器上的命令，主要就是把ssh命令行的指令变成脚本，搜了一下发现paramiko这个库可以简单的实现这一功能，做一个记录

没有安装paramiko的环境可以使用pip3 install paramiko安装一下，需要注意pip的版本最好更新到最新，一开始使用低版本的pip遇到了找不到依赖项的情况，更新了pip之后就好了

anaconda没有遇到安装问题

在ubuntu20的虚拟机上写了个demo.py，内容就是打印"Ubuntu is ready"，虚拟机采用桥接方式，地址为192.168.110.104

```py
import paramiko
# 默认ssh登陆在~目录，所以直接执行了如下命令，正常情况最好用文件绝对地址
cmd = 'python3 demo.py'

# create client object
ssh = paramiko.SSHClient()
# do if missing host key, add new hostname into list
ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
# login (fakeinfo here) port22:ssh port
ssh.connect(hostname='192.168.110.104', username='adin', password='xxxxxxx', port=22)
# return result
stdin, stdout, stderr = ssh.exec_command(cmd)
# read the buffer and decode
print(stdout.read().decode())

ssh.close()

```


