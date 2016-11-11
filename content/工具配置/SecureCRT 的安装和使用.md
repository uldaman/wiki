---
title: SecureCRT 的安装和使用
layout: page
date: 2016-11-11 21:09
---

**SecureCRT** 是一款支持 SSH（SSH1 和 SSH2）的终端仿真程序, 同时支持 Telnet 和 rlogin 协议; 它可用于连接运行包括 Windows、Unis 和 linux 在内的远程系统.

首先下载 SecureCRT 并安装(网上有中文版本下载, 注意找大网站下载, 别下载了被装后门的版本).

去 Ubuntu 中开启 SSH 功能. (SSH 为 Secure Shell 的缩写, 即安全外壳协议, 它是由 IETF 的网络工作小组（Network Working Group）所制定; SSH 是建立在应用层和传输层基础上的安全协议; 它专为远程登录会话和其他网络服务提供安全性的协议.)
在 Ubuntu 中开启 SSH 功能只需要一句命令: sudo apt-get install openssh-server openssh-client

查看 Ubuntu 的 ip, 在终端输入命令: ifconfig(注意不是 ipconfig).

![](http://i62.tinypic.com/fvfr40.jpg)

记录下 ip 地址.

返回 SecureCRT 中, 选择 快速连接.

![](http://i61.tinypic.com/33lfmt1.jpg)

输入 Ubuntu 的 ip 地址和用户名.

![](http://i60.tinypic.com/15zo868.jpg)

点击连接, 会弹出对话框让输入密码, 输入确定即可.

![](http://i57.tinypic.com/6dyec6.jpg)

![](http://i60.tinypic.com/1zoj2hc.jpg)

此时输入中文可能会显示乱码, 这是因为 Ubuntu 的 ssh 默认编码是 utf-8, 而 SecureCRT 却不是, 所以只要把 SecureCRT 的编码改成 utf-8 就可以了.

![](http://i60.tinypic.com/344qskp.jpg)

![](http://i58.tinypic.com/30u3j46.jpg)

SecureCRT连接后如果稍长时间不用就会掉线, 往往会造成工作状态的丢失, 如下设置可以始终保持 SecureCRT 的连接:

![](http://i62.tinypic.com/2uhr0h3.jpg)

![](http://i61.tinypic.com/wl7m88.jpg)

另外还有几个有用的配置:

![](http://i67.tinypic.com/sw6yrt.jpg)

![](http://i63.tinypic.com/34rjfvd.jpg)

现在远程操作环境就搭建好了, 可以在 SecureCRT 中像 Ubuntu 终端那样使用.

![](http://i58.tinypic.com/ics9dd.jpg)
