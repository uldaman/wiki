---
title: [版本控制] 四、Git 的安裝(Windows)
layout: page
date: 2015-06-19 19:40
---

[TOC]

最早 Git 是在 Linux 上开发的, 很长一段时间内, Git 也只能在 Linux 和 Unix 系统上跑, 不过, 慢慢地有人把它移植到了 Windows 上, 现在, Git 可以在 Linux、Unix、Mac 和 Windows 这几大平台上正常运行了.
要使用 Git, 第一步当然是安装Git了, 這裡以 Windows 為例(Why? 因為本屌暫時使用的就是 Windows, 其它系統安裝參考: [傳送門](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137396287703354d8c6c01c904c7d9ff056ae23da865a000)).

首先, 先去下載 Windows 下 Git 安裝包 msysgit([傳送門](http://msysgit.github.io/)), 或者可以自行 google Git.

安裝好後, 運行`Git Bash`, 它是一個類似命令行的窗口, 這是一個 Windows 下模擬的 Linux 環境, 在這個窗口裡可以使用 Linux 的命令.

![](http://i60.tinypic.com/11sfnt0.jpg)

安裝完成後, 還需要設置下本機使用的 Git 賬戶(用戶名和密碼).
這個賬戶是給服務器看的, 因為 Git 是分佈式版本控制系統, 每台電腦都可以 Push 補丁到服務器, 所以服務要就需要知道是誰進行了提交, 這裡的誰就是上面設置的賬戶.

```
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```
<br>
`global` 即全局, 這個參數表示這台機器上所有的 Git 倉庫都會使用這個配置.

你還可以通過 `git config --list` 來查看當前的 config 配置.

* * *

最後, 我們來安裝一下 TortoiseGit, 這和 TortoiseSVN 一樣, 是一個 Git 圖形化操作工具.

先安装 msysgit, 然后安装 TortoiseGit, 因为 TortoiseGit 需要使用到 msysgit 提供的 Git 核心, 如果你弄反了, 也没有关系, 可以进入 TortoiseGit 的设置界面(右键菜单上 TortoiseGit -> Settings -> General -> MSysGit –> Path), 手动指定 Git 核心文件的位置.

如果使用 GitHub 的話, 那還有比 TortoiseGit 更好的先擇, 那就是 GitHub for windows.
