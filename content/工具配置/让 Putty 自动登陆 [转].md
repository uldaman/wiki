---
title: 让 Putty 自动登陆 [转]
layout: page
date: 2016-05-05 14:04
---

[TOC]

### 序言

Putty 基本是用来登陆 Linux/Unix 终端的不二之先, 因其小, 开源, 界面也非常实用. 可是当你要在私有的机器上, 经常性的要登陆很多机器的时候就觉得烦琐了, 不光打开一堆的窗口, 还要一遍一遍的输入用户名和密码. 当然登陆用户名是可以保存的, 我也是最近才注意到这一点的. Putty 不愿去保存密码是出于安全考虑, 相信在多数时候确实是非常必要的.

于是我选择了 SecureCRT, 它能保存密码, 而且是多 Tab 的, 把 Session 改成 VT100 Linux 模式, 用着一直很顺溜. 然而今天却发现在 SecureCRT 终端中使用 vim 不能高亮显示语法, 试过多种设置, 在 Putty 中却行的, 就这样又重新回到了 Putty 的怀抱. 要相处得好, 必须更深层次的挖掘它的潜力, 其实它也可让你实现自动登陆、甚至是多 Tab 的方式.

方法有四~

### 一. 用 PuTTYgen.exe 生成 ssh 证书, 可在特定的机器上自动登陆
PuTTYgen.exe 也是从 [putty.exe 站点](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)下载, 对于 Linux to Linux 的 ssh 证书生成及自动登陆的方法也有一个[介绍](http://unmi.cc/ssh-no-need-input-password).

而怎么使用 PuTTYgen.exe 请直接参考: [SSH证书让Putty免密码登陆Linux](http://www.shocr.com/sshcertificate-putty-linux/).

这种方法需要在服务器端做相应的操作, 本文主要想介绍第二种方法.

### 二. 用 Putty v6.0 修改版, 可保存登陆名和密码, 实现自动登陆
Putty 默认版只能保存登陆名, 每次需输入登陆密码, 主机和登陆名是保存在注册表中的. 不过因为它是开源, 所以有相关需求者做了个能同时保存用户和密码的版本, 这些信息是保存在文件中的. 该保存登陆名和密码版可在些下载: [putty_v6.0.rar](http://unmi.cc/wp-content/uploads/2010/06/putty_v6.0.rar).

就是设置起来有点是特别好理解, 默认版本也是这样子的. 操作步骤全写在下面这幅图片里了:

![](http://i65.tinypic.com/svj5m1.jpg)

可以看看人家是怎么改的 PuTTY 源代码的:  [修改Putty 0.6 代码支持SSH 密码保存功能](http://blog.csdn.net/free2o/article/details/2507023)

### 三. 有了个 PuTTY Connection Manager 管理你的 PuTTY 登陆, 还支持 Tab

PuTTY Connection Manager 的[官网](http://puttycm.free.fr)被 Blocked 了, 想窥个究竟的就勇敢的翻墙吧. 这是用 C# 写的对 PuTTY 的包装程序, 需要机器上安装 .NET Framework 2.0 或以上的版本, 另外, 可让你使用更新 PuTTY 版本. 从官方截个图让大家看看:

![](http://i65.tinypic.com/30jh1e9.jpg)

![](http://i65.tinypic.com/wrgknc.jpg)

也能兼容原来的 PuTTY 保存登陆名的方式, 还可用自己的连接管理独立的保存登陆名和密码, 可多 Tab 使用. 如果你觉得界面复杂, 可以把菜单、工具栏、状态栏什么的全隐藏了起来, 看起来就和 PuTTY 一样清爽了.

为给大家省事, 还是把 PuTTY Connection Manager 的程序从网站上捞下来放到这里来了: [puttycm.rar](http://unmi.cc/wp-content/uploads/2010/06/puttycm.rar). 其中 puttycm.exe 是个独立执行版, 要求你事前安装了 .NET Framework, puttycm0.7.1.136beta.exe 是安装版, 会自动检测有没有 .NET Freamework, 无则帮你下载安上.

### 四. 用快捷方式实现自动登陆

首先创建 putty.exe 的快捷方式到桌面; 然后运行 putty, 输入host name、port、saved session’s name, 点击保存, 假设 session 名为"qa server", 随后关闭窗口; 最后右击 putty 快捷方式, 属性, 目标, 加上如下参数 `-load "qa server" -ssh -l {username} -pw {password}`, 保存, 关闭;

以后运行putty的快捷方式, 就能自动输入用户名和密码, 进入远程电脑.
