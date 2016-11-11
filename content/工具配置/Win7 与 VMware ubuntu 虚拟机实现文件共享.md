---
title: win7 与 VMware ubuntu 虚拟机实现文件共享
layout: page
date: 2016-04-23 14:55
---

[TOC]

貌似安装 VM Tools 后也无法直接拖拽, 只能让 win7 与 VMware ubuntu 虚拟机实现文件共享了~~

## 第一步
当然是安装 VM Tools 啦.

1. 在 VMware 虚拟机界面, 点 VM \-\> Install VMware Tools, 之后, 点击 Ubuntu 窗口中主文件按钮, 然后再左边会看见设备里有 VMware tools 文件夹, 右键 \-\> 解压到这里
2. 打开 Terminal 终端(ctrl + alt + t), 进入解压的文件夹中, 输入以下命令:

```
sudo tar zxf VMwareTools-xxx.tar.gz
cd /vmware-tools-distrib
sudo ./vmware-install.pl
```
<br>
安装过程中一直按 enter 即可, 或者输入 \[\] 提示 yes 和 no, 直到安装完毕

最后查看下结果:

```
lsmod | grep vmblock
```
<br>
## 第二步
设置共享文件夹的目录

1. 将 Ubuntu 关机, 否则不能添加共享文件夹
2. 在 VMware 虚拟机窗口, 选择 VM \-\> Settings \-\> Options \-\> Shared Folders
3. 点右边的 Add, 点 Next \-\> 选择 Win7 共享目录的路径, 然后点 Next \-\> 选中 Enable this share \-\> Finish
4. 在 VM \-\> Settings \-\> Options \-\> Shared Folders 窗口的右边, Folder sharing 栏里选择 Always enabled
5. 点 OK, 重启 Ubuntu

## 第三步
在 Ubuntu 虚拟机下挂载共享文件夹

1. 执行 `sudo apt-get install open-vm-dkms` (注: 如果安装过, 以后就不用执行这一行)
2. 执行 `sudo mount -t vmhgfs .host:/ /mnt/hgfs`
3. 然后去看看效果吧 `cd /mnt/hgfs`

## 第四步
把挂载共享文件夹加入开机动作

`sudo gedit /etc/init.d/rc.local`, 打开的文件中加入挂载代码: `sudo mount -t vmhgfs .host:/ /mnt/hgfs`, 如下图, 保存退出, 重启 Ubuntu, 发现共享文件夹已经成功自动挂载了.

![](http://i68.tinypic.com/2uos3mu.jpg)
