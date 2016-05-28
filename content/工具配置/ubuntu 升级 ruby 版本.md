---
title: ubuntu 升级 ruby 版本.md
layout: page
date: 2016-05-28 21:47
---

[TOC]

# 使用 rvm 管理 ruby 版本
这是推荐的方式, 但是由于 GFW 的原因, rvm 不一定安装成功, 如果不成功则参考下面的从源码安装.

Reference: [在ubuntu中安装及使用rvm管理ruby版本](http://blog.csdn.net/abbuggy/article/details/8170899)

## 准备工作
后面需要使用 **curl**, 用 **dpkg \-s curl** 命令检查一下系统中有没有安装

```
abbuggy@abbuggy-ubuntu:~$ dpkg -s curl
Package: curl
Status: install ok installed
Priority: optional
Section: web
Installed-Size: 338
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Architecture: i386
Version: 7.22.0-3ubuntu4
Replaces: curl-ssl
Provides: curl-ssl
Depends: libc6 (>= 2.7), libcurl3 (>= 7.16.2-1), zlib1g (>= 1:1.1.4)
Description: Get a file from an HTTP, HTTPS or FTP server
 curl is a client to get files from servers using any of the supported
 protocols. The command is designed to work without user interaction
 or any kind of interactivity.
 .
 curl offers a busload of useful tricks like proxy support, user
 authentication, FTP upload, HTTP post, file transfer resume and more.
Homepage: http://curl.haxx.se
Original-Maintainer: Ramakrishnan Muthukrishnan <rkrishnan@debian.org>
```
<br>
上面的说明我是已经安装过了的, 如果没有安装应该是类似如下的回显:

```
abbuggy@abbuggy-ubuntu:~$ dpkg -s curl
系统没有安装软件包 curl, 因而没有相关的信息.
使用 dpkg --info (= dpkg-deb --info) 来检测打包好的文件,
还可以通过 dpkg --contents (= dpkg-deb --contents) 来列出它们的内容.
```
<br>
那么安装就行了 `sudo apt-get install curl`

## 安装 rvm
用 rvm 官方推荐的方式安装 `curl -L get.rvm.io | bash -s stable`

回显提示我们, RVM 被安装在 `$HOME/.vrm` 中, 并且需要在终端中加载脚本 `$HOME/.rvm/scripts/rvm`

```
abbuggy@abbuggy-ubuntu:~$ curl -L get.rvm.io | bash -s stable
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   185  100   185    0     0    144      0  0:00:01  0:00:01 --:--:--   906
100 10235  100 10235    0     0   3929      0  0:00:02  0:00:02 --:--:-- 10888
Downloading RVM from wayneeseguin branch stable
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   131  100   131    0     0     62      0  0:00:02  0:00:02 --:--:--   143
100 1124k  100 1124k    0     0   127k      0  0:00:08  0:00:08 --:--:--  269k

Installing RVM to /home/abbuggy/.rvm/
    RVM PATH line found in /home/abbuggy/.bashrc /home/abbuggy/.zshrc.
    RVM sourcing line found in /home/abbuggy/.bash_profile /home/abbuggy/.zprofile.

# RVM:  Shell scripts enabling management of multiple ruby environments.
# RTFM: https://rvm.io/
# HELP: http://webchat.freenode.net/?channels=rvm (#rvm on irc.freenode.net)
# Cheatsheet: http://cheat.errtheblog.com/s/rvm/
# Screencast: http://screencasts.org/episodes/how-to-use-rvm

# In case of any issues read output of 'rvm requirements' and/or 'rvm notes'

Installation of RVM in /home/abbuggy/.rvm/ is almost complete:

  * To start using RVM you need to run `source /home/abbuggy/.rvm/scripts/rvm`
    in all your open shell windows, in rare cases you need to reopen all shell windows.

# abbuggy,
#
#   Thank you for using RVM!
#   I sincerely hope that RVM helps to make your life easier and more enjoyable!!!
#
# ~Wayne
```
<br>
应该请把这句话加在 `$HOME/.bash_profile` 文件中, 以便在开启一个终端会话时候加载 RVM:

```
[[ -s "$HOME/.rvm/scripts/rvm" ]] && . "$HOME/.rvm/scripts/rvm" # This loads RVM into a shell session.
```
<br>
如果对 shell 不熟悉, 这里简单解释一下这是干什么用的:<br>
`[[condition]]` 两层的方括号中间括着条件返回条件是不是真, \-s 是判断给定的文件是否存在的命令.<br>
这样一来, 不就是在 `[[ -s "$HOME/.vrm/scripts/vrm"]]` 判断刚才安装的 RVM 是否存在吗?<br>
接下来的&&符号是**"短路的与"**, 当前面的条件是真的时候, 执行后面的语句, 返回这两个语句是不是全是真.<br>
在这里, 利用了"短路"特性, 也就是说当 RVM 已经安装的话, 执行后面的 `$HOME/.rvm/scripts/rvm` 命令.<br>
这条命令和 `source "$HOME/.rvm/scripts/rvm"` 是一个意思: 加载 rvm 的启动脚本.

现在, 通过修改 `.bash_profile` 增加的内容需要重新打开终端窗口时加载, 或者另一个是在当前窗口执行一遍 `$HOME/.rvm/scripts/rvm`.

##使用 RVM
察看 RVM 的版本

```
rvm -v

rvm 1.16.20 (stable) by Wayne E. Seguin <wayneeseguin@gmail.com>, Michal Papis <mpapis@gmail.com> [https://rvm.io/]
```
<br>
然后通过 `rvm requirements` 命令, 可以察看安装各版本时候的前提条件, 其中现在这句是需要关注的, 这是安装依赖的第三方包, 没有这个安装不成功岂不是很悲剧?

```
Additional Dependencies:
# For Ruby / Ruby HEAD (MRI, Rubinius, & REE), install the following:
  ruby: /usr/bin/apt-get install build-essential openssl libreadline6 libreadline6-dev curl git-core zlib1g zlib1g-dev libssl-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt-dev autoconf libc6-dev ncurses-dev automake libtool bison subversion pkg-config
```
<br>
这样一来, 安装 ruby 的准备工作就完成了, 察看当前 RVM中 已经安装的 ruby 版本, 现在应该还没有.

```
rvm list

rvm rubies

# No rvm rubies installed yet. Try 'rvm help install'.
```
<br>
察看 RVM 可供安装的 ruby 版本:

```
rvm list known
# MRI Rubies

[ruby-]1.8.6[-p420]
[ruby-]1.8.7-p370
[ruby-]1.8.7[-p371]
[ruby-]1.9.1[-p431]
[ruby-]1.9.2-p180
[ruby-]1.9.2-p290
[ruby-]1.9.2-p318
[ruby-]1.9.2[-p320]
[ruby-]1.9.2-head
[ruby-]1.9.3-preview1
[ruby-]1.9.3-rc1
[ruby-]1.9.3-p0
[ruby-]1.9.3-p125
[ruby-]1.9.3-p194
[ruby-]1.9.3-p286
[ruby-]1.9.3-[p327]
[ruby-]1.9.3-head
[ruby-]2.0.0-preview1
```
<br>
例如安装 ruby 1.9.3-head, 在不发生歧义的情况下方括号内的东西可以不必敲.

```
rvm install 1.9.3-head
```
<br>
之后等呀等呀自动安装了 1.8.7 和 1.9.3

```
rvm list

rvm rubies

   ruby-1.8.7-p371 [ i686 ]
   ruby-1.9.3-head [ i686 ]

# Default ruby not set. Try 'rvm alias create default <ruby>'.
```
<br>
选择 1.9.3 作为当前的使用版本, 并且设置为缺省

```
rvm use ruby-1.9.3-head --default
Using /home/abbuggy/.rvm/gems/ruby-1.9.3-head
```
<br>
设置好之后察看ruby版本

```
ruby -v
ruby 1.9.3p327 (2012-11-10) [i686-linux]
```
<br>
察看ruby的路径, 就是 RVM 帮我们安装的

```
which ruby
/home/abbuggy/.rvm/rubies/ruby-1.9.3-head/bin/ruby
```
<br>
# 从源码安装
如果你不能安装 RVM, 那就尝试从源码安装吧.

Reference: [Setup Ruby On Rails on Ubuntu 14.04 Trusty Tahr](http://blog.csdn.net/abbuggy/article/details/8170899)

为避免和当前 Ruby 冲突, 先卸载当前版本:

```
sudo apt-get remove ruby1.8

sudo apt-get autoremove
```
<br>
然后安装新版本的 Ruby:

```
wget http://ftp.ruby-lang.org/pub/ruby/2.3/ruby-2.3.1.tar.gz

tar -xzvf ruby-2.3.1.tar.gz

cd ruby-2.3.1/

./configure

make

sudo make install
```
<br>
安装完查看下版本: `ruby -v`.

# 使用 Travis Client 推荐的方法
由于我使用第二种方法成功安装了, 因为这种方法我没测试过, 先记录下再说...

Reference: [Travis Client Github](https://github.com/travis-ci/travis.rb#other-unix-systems)

先卸载当前版本:

```
sudo apt-get remove ruby1.8

sudo apt-get autoremove
```
<br>
然后安装新版本:

```
sudo apt-get install python-software-properties
sudo apt-add-repository ppa:brightbox/ruby-ng
sudo apt-get update
sudo apt-get install ruby2.1 ruby-switch ruby2.1-dev
sudo ruby-switch --set ruby2.1
```
