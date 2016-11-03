---
title: SSH 远程执行脚本的环境变量问题
layout: page
date: 2016-11-03 23:59
---

[TOC]

export 导出变量的作用域只即在当前进程及其子进程.

SSH 远程会话是没有远程主机的环境变量, 解决方法: `ssh 远程主机 ". ~/etc/profile && 命令"`.

有兴趣深纠可参考: [ssh 连接远程主机执行脚本的环境变量问题](http://feihu.me/blog/2014/env-problem-when-ssh-executing-command-on-remote/#section-10)
