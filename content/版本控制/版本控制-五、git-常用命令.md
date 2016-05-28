---
title: 五、Git 常用命令
layout: page
date: 2015-06-20 08:05
---

[TOC]

<blockquote>這裡僅說明下`git status`命令和 Ignore file, 關於 Git 的其它命令參考[廖老師的在線教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000).</blockquote>

![](http://i57.tinypic.com/b7kwgz.jpg)

**`git status`**命令可以让我们时刻掌握仓库当前的状态, 下面來看看使用該命令出現的提示信息各代表什麼意思:

- **Untracked files:** 未跟蹤文件, 意味著當前的 Git 快照中沒有這些文件, 此時可以使用 `git add` 命令來添加跟蹤.
- **Changes not staged for commit:** 表明被跟蹤的文件已经修改, 但是还没有放入暫存區, 此時有兩種情況:
    - 想提交修改, 這時可先使用 `git add` 添加到暫存區, 然後再使用 `git commit –m "註釋"` 提交到本地版本庫(或者可以直接使用 `git commit –a –m "註釋"` 結合這兩步).
    - 丟棄更改, 想回退成初始狀態, 這時可使用 `git checkout -- 文件名` 來丟棄工作區的修改, 是让这个文件回到最近一次 `git commit ` 或 `git add` 时的状态.
- **Changes to be committed:** 這表示當前已經被提交到暫存區, 但是還沒有提交, 此時也有兩種情況:
    - 想提交修改, 直接使用 `git commit –m "註釋"` 提交到本地版本庫.
    - 想丟棄更改, 這時要先使用 `git reset HEAD file` 把暫存區的修改撤銷掉, 然後再使用 `git checkout -- 文件名` 來丟棄工作區的修改.

* * *

**Ignore file,** 即忽略文件.

這是一個名為 `.gitignore` 的文件, 裡面保存的是要忽略的文件.

由於 Windows 的特性, 可能無法直接新建這樣文件名的文件, 我們可以在 `Git Bash` 中使用 Linux 命令(vi .gitignore)來創建這個文件.

配置语法:

- 以 “/” 开头表示目录
- 以 “?” 通配单个字符
- 以 “*” 通配多个字符
- 以方括号 “[]” 包含单个字符的匹配列表
- 以叹号 “!” 跟踪某个文件或目录

例如在 `.gitignore` 寫入 test.lib 後, 那麼 Git 將忽略名為 test.lib 的文件的任何狀態及修改;<br>
寫入 /test(或者 /test/*) 就能忽略根目錄下的 test 文件夾(包括這個文件夾下的所有內容).
