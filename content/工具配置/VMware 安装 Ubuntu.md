---
title: VMware 安装 Ubuntu
layout: page
date: 2016-10-16 03:04
---

[TOC]

# 安装系统
**1. 创建新的虚拟机**

![](http://i59.tinypic.com/2jff68n.jpg)

**2. 使用自定义模式**

![](http://i60.tinypic.com/315ohh1.jpg)

**3. 下一步直到选择安装源**

![](http://i58.tinypic.com/33vp5af.jpg)

**4. 选择操作系统**

![](http://i62.tinypic.com/2lw6dz9.jpg)

**5. 设置虚拟机名称及安装路径**

![](http://i61.tinypic.com/t6e0c2.jpg)

**6. 设置处理器**

![](http://i60.tinypic.com/16gg5co.jpg)

**7. 设置内存, 对于测试环境的 Linux (32 位) 来说, `1G` 足够 (条件允许可以设置大一点).**

![](http://i58.tinypic.com/16c2e6u.jpg)

**8. 设置网络类型, 如果使用有线上网, 推荐 `NAT`, 如果是无线的话, 推荐使用桥接.**

![](http://i58.tinypic.com/2d0ayw4.jpg)

**9. 下面三步直接默认的就可以了.**

![](http://i58.tinypic.com/34xjr52.jpg)

![](http://i57.tinypic.com/2rn7zeu.jpg)

![](http://i62.tinypic.com/5ufhnp.jpg)

**10. 设置磁盘, 使用默认的 `20G` 足够了, 下面选择第一个单选框.**

![](http://i60.tinypic.com/2w20m6q.jpg)

**11. 设置磁盘文件及完成创建, 都默认就可以了.**

![](http://i58.tinypic.com/ol15l.jpg)

![](http://i60.tinypic.com/msit82.jpg)

**12. 点击编辑虚拟机设置.**

![](http://i61.tinypic.com/2i7tvnb.jpg)

**13. 设置光盘镜像后点击确定.**

![](http://i57.tinypic.com/33m7i3q.jpg)

**14. 点击开启此虚拟机.**

![](http://i60.tinypic.com/5xsfgo.jpg)

**15. 等待虚拟机启动, 直到设置语言, 选择语言后点击安装 Ubuntu.**

![](http://i62.tinypic.com/nc1209.jpg)

**16. 对于我们编程或测试来说下面两个不必要勾选, 直接点继续.**

![](http://i60.tinypic.com/ergcxx.jpg)

**17. 选择最后一项, 我们将自定义分区设置.**

![](http://i62.tinypic.com/2u6pbbd.jpg)

**18. 现在开始利用上一篇学到的东西开始定义我们的分区.**

**18.1. 选中 `/dev/sda` 后点击新建分区表…此时会弹出一个对话框, 直接确定就可以了.**

![](http://i61.tinypic.com/1zlqiwg.jpg)

**18.2. 选中新出现的空闲, 再点击左下方的 `+`.**

![](http://i60.tinypic.com/j5e5xc.jpg)

**18.3. 下图中的 "主分区"，"空间起始位置"， "Ext4 ..." 均为默认, 不需要修改; 大小设置为 `10240 MB = 10G`; 最后的 "挂载点" 下拉列表中, 选中 "/", 点 "确定".**

![](http://i62.tinypic.com/muv1nb.jpg)

**18.4. 同 `18.2` 中一样**

![](http://i57.tinypic.com/2uy0v3t.jpg)

**18.5. 大小选为 `1024 MB = 1G`, 用于下拉框选择交换空间 (即 swap). 这里我们没有用上一篇说过的 `2 倍`, 因为是虚拟机测试环境, 所以选 `1G` 就可以了.**

![](http://i57.tinypic.com/20k4rr9.jpg)

**18.6. 同 18.2 中一样**

![](http://i61.tinypic.com/ivjml5.jpg)

**18.7. 大小设为 `200 MB`, 挂载点选为 `/boot`.**

![](http://i62.tinypic.com/2ef25pi.jpg)

**18.8. 同 18.2**

![](http://i62.tinypic.com/24e6vs7.jpg)

**18.9. 挂载点选为 `/home`, 其它默认就可以了.**

![](http://i60.tinypic.com/2chkjlu.jpg)

**18.10. 此时你的设置应该如下, 点现在安装即可.**

![](http://i59.tinypic.com/2usfjw0.jpg)

**19. 下面的步骤默认就可以了, 不过要注意的是要记得设置的账号和密码.**

![](http://i57.tinypic.com/9v89k4.jpg)

![](http://i57.tinypic.com/6gy1r7.jpg)

![](http://i59.tinypic.com/aljcaq.jpg)

![](http://i61.tinypic.com/23u6jpy.jpg)

# 安装 VM Tools
全部安装完成以后, 我们还需要安装 **VM Tools** 来提高虚拟机使用性.

**1. 先关闭虚拟机, 将 CD 设置成使用物理驱动器. 再启动虚拟机, 进入安装好的 Ubuntu 系统后, 选择虚拟机菜单栏 \-\- 安装 VMware tools.**

![](http://i61.tinypic.com/29fw7tg.jpg)

![](http://i60.tinypic.com/2vxo9x0.jpg)

**2. 在 Ubuntu 系统中弹出的 VMware tools 窗口中找到 `VMwaretools-9.6.0-1294478.tar.gz` 复制到桌面.**

![](http://i60.tinypic.com/25gdlia.jpg)

**3. 复制完之后, 右键左侧栏的文件夹图标 \-\- 退出, 这时应该返回到桌面界面了. 在桌面上的 `VMwaretools-9.6.0-1294478.tar.gz` 右击 提取到此处.**

![](http://i60.tinypic.com/2vwfbzo.jpg)

**4. 此时桌面就多了一个 `vmware-tools-distrib` 的文件夹, 按 `CTRL+ALT+T` 呼出终端. 为了以后方便, 此时可以在左侧栏的终端图标上右键 \-\- 锁定到启动器.**

![](http://i60.tinypic.com/a0h0so.jpg)

**5. 在弹出的终端窗里输入 `sudo su` 进入 "超级用户"模式, 此时它会要求输入密码, 输入即可, 要注意, 在终端输入密码是不会显示的, 输完后直接确定就可以了.**

![](http://i62.tinypic.com/21oo66b.jpg)

**6. 输入 `ls` 命令列出文件夹里的文件, 然后进入 使用 `cd` 桌面 命令进入桌面文件夹.**

![](http://i61.tinypic.com/jgtq9t.jpg)

**7. 输入 `ls` 命令列出桌面上的文件, 然后进入 使用 `cd` 命令进入刚才解压出来的文件夹.**

(小技巧, 因为 `vmware-tools-distrib` 名字很长, 可以输入名字的几个字母后按 `tab` 键, 终端会自动补全; 当然也可以在 `ls` 显示出来的结果上直接复制然后粘贴.)

![](http://i57.tinypic.com/20ayo0l.jpg)

**8. 进入 `vmware-tools-distrib` 后, 使用 `sudo ./ vmware-install.pl` 命令安装 VM Tools. 然后一堆的 `enter`、 `y`、 `n` 要按.**

![](http://i58.tinypic.com/5uo8dl.jpg)

安装完后要重启才能生效, 不过我们先别急着重启, Ubuntu 自带的 **iBus** 输入法太难用了, **Fcitx** 才是当之无愧的最好的中文输入法 (框架). 现在我们来安装它.

# 安装 Fcitx
Ubuntu 16 后已自带 Fcitx, 但是没有**五笔**, 需要额外执行 `sudo apt-get install fcitx-table-wubi` 安装下就行了.

Ubuntu 16 以前参考下面的步骤.

在终端输入 `sudo apt-get install fcitx-table-wbpy`.

![](http://i62.tinypic.com/zisi6h.jpg)

安装完成之后, 输入 `reboot` 重启系统.

重启系统过后, 首先拖动虚拟机大小看看屏幕能不能自适应, 如果可以的话说明 **VM Tools** 安装好了.

接下来看看刚安装好的输入法.

首先打开语言设置.

![](http://i58.tinypic.com/2m7vo.jpg)

在 *键盘输入方式系统* 处可以看到已经变成 **fcitx** 了, 如果不是的话, 手动选择下再重启.

![](http://i60.tinypic.com/wsv1pd.jpg)

此时, 你的系统应该有双拼、拼音、五笔拼音等乱七八糟的输入法. 如果只需要一个五笔拼音就够了, 可以从 Ubuntu 右上角顶栏的小键盘图标中打开配置，如下图：

![](http://i59.tinypic.com/11ljxie.jpg)

然后在桌面新建一个文本文档测试下新输入法, 是不是感觉棒棒哒..

![](http://i62.tinypic.com/349citu.jpg)

Ubuntu 还有很多不错的软件工具可以使用, 可以到 [http://wiki.ubuntu.org.cn/Qref/Apps](http://wiki.ubuntu.org.cn/Qref/Apps) 自己找~

看到合适的心仪的工具后, 直接点右侧的安装就可以了(当然这必须在 Ubuntu 里的浏览器上操作, 在 Windows 下可不行).

![](http://i60.tinypic.com/10n85s9.jpg)
