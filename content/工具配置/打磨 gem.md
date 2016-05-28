---
title: 打磨 gem
layout: page
date: 2016-05-28 22:56
---

[TOC]

# 打磨
由于 GFW 的原因, 直接使用 gem 会报错: `Errno::ECONNRESET: Connection reset by peer - SSL_connect (https://api.rubygems.org`

更改 gem 的源为国内的映像就好了:

```
gem sources --remove https://rubygems.org/

gem sources -a https://ruby.taobao.org/
```
<br>

查看下当前源:

```
$ gem sources -l
*** CURRENT SOURCES ***

https://ruby.taobao.org
```
<br>
一定要**确保**只有 `ruby.taobao.org`

# 注意事项
gem 的配置是区分 sudo 和普通用户命令的...

也就是说, 如果你用上面的方法打磨后, 如果使用 `sudo gem sources -l` 会发现还是以前的 `https://rubygems.org/`.

所以用 sudo 再执行次上面的配置:

```
sudo gem sources --remove https://rubygems.org/

sudo gem sources -a http://ruby.taobao.org/

sudo gem sources -l

*** CURRENT SOURCES ***

http://ruby.taobao.org
```
<br>
