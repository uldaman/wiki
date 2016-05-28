---
title: [版本控制] 三、Git 初識
layout: page
date: 2015-06-18 18:20
---

[TOC]

首先推薦一個在線 Gti 學習網站: [傳送門](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

另外, 还有个 Google Code 的声明也可以看看: [再見, Google Code, 你好, GitHub](http://www.infoq.com/cn/news/2015/03/goodbye-google-code-hello-github)

還提供一個 Git Book 的中文 PDF: [http://yunpan.cn/cQF9zES6Zt5d5](http://yunpan.cn/cQF9zES6Zt5d5)  访问密码 7e81

![](http://i59.tinypic.com/2hd0d40.jpg)

Git 的開發流程大概如上圖, Git 非常鼓勵大家使用分支來工作.

# Git 的特點

- Git 的版本控制都是通過快照的形式(磁盤快照), 而不是記錄文件變化.
- 幾乎所有的操作都是本地的, 只有當所有的倉庫需要合併的時候再聯上網.
- Git 文件狀態的生命週期示意圖:

![](http://i59.tinypic.com/21c5hsj.jpg)

# Git 環境變量
Git 安裝目錄下有個 etc 目錄, 裡面有個 gitconfig 文件, 它保存的就是 Git 的配置信息, 包括環境變量.

可以通過一些命令來操作 Git 配置, 如: 設置身份

```
$ git config --global user.name "xx"
$ git config --global user.email "xxxx.xxx"
```
<br>
其他的命令可以使用 `$ git config --help` 自行搜索.

# Git 中的一些基本概念
- Blessed(Remote) repostory: 遠程倉庫
- local repostory: 本地倉庫
- stage area: 臨時區(相當於 SVN 中的 Add 那種, 但是也有很大的區別, SVN 是新添加的文件需要 Add, 而 Git 中, 任何文件, 包括新文件和修改的, 都需要先提交的臨時區, 再提交到本地庫.)
- workspace: 工作區
