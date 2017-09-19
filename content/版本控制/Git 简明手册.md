---
title: Git 简明手册
layout: page
date: 2016-03-13 13:00
---

[TOC]

## 1. git 配置

- git config \-\-list
- git config \-\-global user.name "your name"
- git config \-\-global user.email "email@example.com"
- git config \-\-global core.autocrlf  false // 关闭换行符检测, windows 的换行有点问题

## 2. 初始化版本库
打开 git bash, 进入目标目录, 执行 **git init**, 在目标目录下就会生成 **.git** 目录, 它存放的就是 git 版本库信息.

## 3. 正常工作流程
edit file (工作区) \-\> git add (暂存区/index/stage) \-\> git commit (commit)

- git add file1 file2
    + 如果想将当前目录所有文件都加入 index, 可以用 git add . (点表示当前目录)
- git diff \-\-cached 可以查看哪些文件将被 commit, 即 index 里的文件
    + 或者可以用 git status 查看当前版本库的状况
- git commit \-m "注释"

> 如果不用 git add 命令, 可以用 git commit -a 参数来合并 add 和 commit 操作(不包括未加入版本控制的文件, 即新建立的文件).

## 4. 分支
- 创建一个新分支
    + `git branch` 分支名
- 查看本地分支表
    + `git branch`, * 号表示当前所在分支
- 查看所有分支表(包括远程)
    + `git branch -a`
- 切换分支
    + `git checkout` 分支名
- 合并分支
    + `git checkout master`, `git merge` 分支名, 这样就把分支的内容合并的 master 分支了
- 解决冲突
    + 如果产生了冲突, 先 `git diff` 查看冲突, 解决冲突后, `git add`, `git commit` 重新提交
- 查看图形化时间线
    + `gitk`
- 比较分支
    + `git diff` 分支名..分支名
- 重命名本地分支
    + `git branch -m` 原名 新名
- 删除本地分支
    + `git branch -d` 分支名, 删除已经合并的分支
    + `git branch -D` 分支名, 强制删除分支, 不管有没有合并过
- 删除远程分支
    + 方法一: `git push --delete origin branch-name`
    + 方法二: `git push origin :branch-name`
- 清理本地的远程分支追踪 (使用场景: 某一个远程分支被另一个开发删除)
    + 方法一: `git fetch -p`
    + 方法二: `git remote prune origin`

## 5. 撤销
撤销的情况比较复杂

首先是 **reset \-\-参数 commit id** 命令, 这个命令有三个参数:

- \-\-mixed：此为默认方式, 回退到某个版本, 只保留工作区, 回退 commit 和 index
- \-\-soft：回退到某个版本, 只回退了 commit, 保留工作区和 index
- \-\-hard：彻底回退到某个版本, 工作区、commit 和 index 都会改变

再就是 **HEAD** '指针', HEAD 表示当前版本, 上一个版本就是 HEAD^, 上上一个版本就是 HEAD^^, 当然往上100个版本写 100 个 ^ 比较容易数不过来, 所以写成 HEAD~100;

另外还有个 **ORIG_HEAD** 指针, 它指向的是 HEAD 上一次所在的位置.

使用 **reset** 命令, 就可以在各个版本之间穿梭了.<br>穿梭前, 用 **git log** 可以查看提交历史, 以便确定要回退到哪个版本, 使用 **\-\-pretty=oneline** 参数可以比较清晰的看到 log, 不过要注意, 一但回到某个版本, 那该版本之后的那些版本通过 git log 就看不到了, 如果还想要重返未来, 可以用 **git reflog** 查看命令历史, 就可以得到'未来'的 commit id 了.

### 未 commit
#### --版本层面上
如果你修改了工作区, 甚至已经提交到 index, 只要你还没 commit, 就可以通过 **git reset \-\-hard HEAD** 可以回退到当前版本的初始状态.

#### --文件层面上
还有一种情况, 就是修改了工作区, 提交到了 index, 然后又修改了工作区, 然后, 只需要撤掉第二次工作区的修改, 如果使用 git reset \-\-hard HEAD 命令, 那就会把工作区和 index 都给撤掉了, 我们可以使用 **checkout** 命令.

命令 **git checkout \-\- readme.txt** 意思就是, 把 readme.txt 文件在工作区的修改全部撤销, 它可以让这个文件回到最近一次 git commit 或 git add 时的状态.

### 已 commit
#### --版本层面上
如果已经 commit, 有**两种**方式来撤销.

##### a. 修改或删除一个提交 (未 push)
使用 `git commit --amend` 可以将本次修改追加到上一次的提交, 还可以通过 `-c` 指定要追加的 `commit id`.

如果确实要删除, 可以通过 **git reset \-\-hard ORIG\_HEAD** 或者 **git reset \-\-hard HEAD^** 来恢复到上一个版本.

##### b. 创建一个撤销了上次提交的新提交 (已 push)
**git revert commit id**, 这个命令将 commit id 的提交撤销(即回退到上一个版本), 然后创建一个新的提交, 这是一种比较安全的作法, 推荐使用.

所以这里要注意下, 假设现在有两个版本, A 版本 和 B 版本, A 是刚提交的版本, B 是上一个版本, 如果想撤销 A 的提交.

- 用 **reset** 的话, 直接指定 B 版本的 commit id, 它会直接回退到 B 版本
- 用 **revert** 的话, 指定的是 A 版本的 commit id, 它会将 A 版本的上一个版本做为一个新提交

注意, revert 后其实并不会直接 commit, 而是保存在 index 中, 需要执行 git commit 才是真正的提交.

在 revert 下, 如果已经修改了当前工作区, 那就有可能和撤销后的版本有冲突, 需要解决冲突后, 再提交.

#### -- 文件层面上
首先查看该文件的历史版本信息: git log default.txt

记录下需要恢复的 commit 版本号, 如: 9aa51d89799716aa68cff3f30c26f8815408e926

恢复该文件: **git reset \-\-hard 9aa51d89799716aa68cff3f30c26f8815408e926 default.txt**

提交git: git commit \-m "恢复单个文件"

## 6. 比较差异
之前也提到一些, 现在单独列出来:

- git diff 查看工作区与 index 的差异 (产生冲突时可以用该命令查看)
- git diff \-\-cached, 查看 index 和 commit 的差异
- git diff 分支名..分支名, 比较分支
- git diff HEAD 查看 工作区 和 commit 的差异
- git diff 分支名, 查看工作区与另一个分支的差异

### 整合 beyond compare 与 diff
安装好 BCompare 后, 打开用户目录~下的 `.gitconfig` 文件, 进行如下修改:

```
[diff]
    tool = bc3
[difftool]
    prompt = false
[difftool "bc3"]
    cmd = "\"D:/martin/i/l/u/Beyond Compare/BCompare.exe\" \"$LOCAL\" \"$REMOTE\""

[merge]
    tool = bc3
[mergetool]
    prompt = false
    keepBackup = false
[mergetool "bc3"]
    cmd = "\"D:/martin/i/l/u/Beyond Compare/BCompare.exe\" \"$LOCAL\" \"$REMOTE\" \"$BASE\" \"$MERGED\""
    trustExitCode = true
```
<br>
参数说明:

- **prompt**: 打开 tool 前是否出现提示 "是否打开 xx ?", 一般不需要
- **keepBackup**: 解决冲突时会生成一个原来冲突文件的备份, 一般也不需要
- **trustExitCode**: 是否接受 tool 的成功解决冲突通知, 如果设为 false, 解决冲突后 git 还会再问一次 "是否解决冲突了?", 简直多余, 所以也不需要

三路合并:

- **LOCAL**: your file with the changes you’ve made to it
- **REMOTE**: the file you’re merging in, possibly authored by someone else
- **BASE**: the common ancestor file that LOCAL and REMOTE came from
- **MERGE_RESULT**: the file resulting from the merge where you resolve conflicts

You often need to see all four of these pieces of information to make intelligent choices. Where you came from (LOCAL), where the other person’s changes came from (REMOTE), where you both started (BASE) and where you are now (MERGE_RESULT).

**下面是之前的方法, 现在已经不用了, 仅留个备份.**

首先在 `~/bin/` 目录下 (windows 是用户主目录, 如 `C:\Users\Administrator`) 新建一个 git-diff-wrapper.sh 文件

window 系统的 git bash 的 git\-diff\-wrapper.sh

```
#/bin/sh
# diff is called by git with 7 parameters:
# path old-file old-hex old-mode new-file new-hex new-mode
"D:/martin/i/l/u/Beyond Compare/BCompare.exe" "$2" "$5" | cat
```
<br>
mac 系统的 git\-diff\-wrapper.sh

```
#/bin/sh
# diff is called by git with 7 parameters:
# path old-file old-hex old-mode new-file new-hex new-mode
bcompare "$2" "$5"|cat
```
<br>
注意：你在 mac 系统中装了 beyong compare 后需要在菜单栏点击 “Install Command Line Tools” 把它加入到命令行.

在用户目录~下修改 .gitconfig 文件, 添加一行:

```
[diff]
external = ~/bin/git-diff-wrapper.sh
```
<br>
注意: Mac上运行 git diff 时, 由于系统要保存历史文件到临时目录再进行比较, 而在保存到临时目录又需要很高的权限, 所以在 git  diff 前加了 sudo 才能执行成功.

## 7. 忽略文件
即 .gitignore, 由于 windows 的特性, 可能无法直接新建这样文件名的文件, 可以在 git bash 中使用 linux 命令来新建.

.gitignore 文件只对还没有加入版本管理的文件起作用, 如果之前已经在之前已经把这些文件加入了版本库, 忽略文件就不起作用了, 那么解决方法就是先把本地缓存删除(改变成未 track 状态), 然后再提交:

    git rm -r --cached .
    git add .
    git commit -m 'update .gitignore'

这并不会改变已有的版本库历史信息, 可以放心使用.

配置语法:

- 以 "#" 开头的是注释
- 以 "/" 开头表示目录
- 以 "?" 通配单个字符
- 以 "*" 通配多个字符
- 以方括号 "[]" 包含单个字符的匹配列表, 如忽略所有的 \*.txt 和 \*.html 文件, *.[txthtml]
- 以叹号 "!" 强制跟踪某个文件或目录

例如在 .gitignore 写入 test.lib 后, 那么将忽略 test.lib 文件, 写入 /test(或者 /test/*) 就能忽略根目录下的 test 目录(包括这个目录下的所有內容).

## 8. 远程操作
### 8.1 新建/克隆版本库
根据情况不同, 有两种操作.

#### 8.1.1 新建版本库
现在的情景是, 你已经在本地创建了一个 Git 仓库后, 又想在 GitHub 创建一个 Git 仓库, 并且让这两个仓库进行远程同步, 这样, GitHub 上的仓库既可以作为备份, 又可以让其他人通过该仓库来协作, 真是一举多得.

首先, 你要去 GitHub 上建立一个新的空版本库, 最好与本地的仓库同名, 好, 现在假设你已经建好了, 新库的地址为: git@github.com:xxx/yyy.git

现在, 在本地打开 git bash(注意, 要进入本地待同步的仓库目录下), 执行下面的命令:

```
git remote add origin git@github.com:xxx/yyy.git
```

这个命令可以理解成, 给远程仓库(git@github.com:xxx/yyy.git)指定一个简名 origin, 以后在本地仓库目录下, 操作 origin 即是操作远程仓库, origin 是默认的叫法, 也可以改成别的, 但是 origin 这个名字一看就知道是远程库, 通常情况下, 我们也是用这个名字.

下一步, 就可以把本地库的所有内容推送到远程库上:

```
git push -u origin master:master
```

推送到远程, 用 **git push** 命令.

```
git push <远程主机名> <本地分支名>:<远程分支名>
```

如果省略远程分支名, 则表示将本地分支推送与之存在”追踪关系”的远程分支, 通常两者同名, 如果该远程分支不存在, 则会被新建;

**-u** 参数相当于建立关联(追踪)关系, 将远程主机(这里是 origin)的 master 分支和本地仓库的 master 分支关联起来, 以后进行推送/拉取操作时就可以简化命令.<br>**如果你使用 branch \-a 来查看当前所有分支, 则会看到这个关联(追踪)关系**.

```
$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
```

> remotes/origin/HEAD is the default branch for the remote named origin. This lets you simply say origin instead of origin/master.

[关于 git push 命令的详细参考链接](http://www.yiibai.com/git/git_push.html)

推送成功后, 可以立刻在 GitHub 页面中看到远程库的内容已经和本地一模一样;

从现在起, 只要本地作了提交, 就可以通过命令:

```
git push
```

把本地 master 分支的最新修改推送至 GitHub, 现在, 你就拥有了真正的分布式版本库.

#### 8.1.2 克隆版本库
8.1.1 讲了先有本地库, 后有远程库的时候, 如何关联远程库, 现在, 假设已经有了一个远程库, 从远程库克隆到本地, 该如何操作.

```
$ git clone git@github.com:xxx/yyy.git
```

如果是 clone 的方式, 那么, 我们就不需要手动去设置远程仓库的简名以及关联(追踪)关系了, 它会自动给它设置好, 我们可以通过 git config --list 查看.

当然, 推送修改时, 你依然可以用 **-u** 参数来指定关联:

```
git push -u origin master:master
```

### 8.2 拉取更新(pull)
该操作取回远程主机某个分支的更新, 再**与本地的指定分支合并**.

[参考链接](http://www.yiibai.com/git/git_pull.html)

```
git pull <远程主机名> <远程分支名>:<本地分支名>
```

如果我们设置了关联(追踪)关系(参考 8.1.1), 则可以省略命令, 直接写: **git pull** 就可以了.

### 8.3 获取更新(fetch)
该操作取回远程主机某个分支的更新, 但并**不与本地分支进行合并**.

[参考链接](http://www.yiibai.com/git/git_fetch.html)

```
git fetch <远程主机名> <分支名>
```

所取回的更新, 在本地主机上要用 "**远程主机名/分支名**" 的形式读取, 比如 origin 主机的 master, 就要用 origin/master 读取.

git branch -a 查看所有分支:

```
$ git branch -a
* master
  remotes/origin/master
```

上面命令结果表示, 本地主机的当前分支是 master, 远程分支是 origin/master.

```
git log -p master..origin/master
```

取回远程主机的更新以后, 通过上面的命令比较本地仓库和远程参考的区别, 如果你需要详细的比较信息, 可以用 diff 命令:

```
git diff master..origin/master
```

当检查修正完后, 使用 git merge 在本地分支上合并远程分支:

```
$ git merge origin/master
```

上面命令表示在当前分支上, 合并 origin/master.

## 9. Git In Windows
首先, 先安装 Msysgit 和 SourceTree, 因为服务器在国外, 下载起来非常慢, 我下载好一份放在云盘上, 地址在下面:

Git For Windows(Msysgit):<br>[https://yunpan.cn/cYR2UcrFIwrKM](https://yunpan.cn/cYR2UcrFIwrKM) 访问密码 1d91

SourceTree:<br>[https://yunpan.cn/cYR2i2zBPbzce](https://yunpan.cn/cYR2i2zBPbzce) 访问密码 45cd

> 除了 SourceTree 外也还有其他的选择, 如 TortoiseGit, 然而试用过后, 还是 SourceTree 比较好.

Git For Windows(Msysgit) 自带一个 **git bash** 和 **git gui**, 哦, 对了, 还有一个 **gitk**.

- git bash 就是一个模拟 linux 的终端
- git gui 是一个轻量级的图形操作界面
- gitk  是一个 git 浏览器, 主要用于用户查看仓库的各类信息(更改信息、提交信息、版本信息、图形显示等)

Msysgit 的图形化界面, 虽然能满足日常需求, 但确实比较粗糙, 而 SourceTree 就是一个很好的替代方案, 日常操作就用 SourceTree, 需要用到命令行时就用 git bash.

其实 SourceTree 也可以自动下载 git, 只不过速度不敢恭维, 还是我们自己手动下载、安装好了, 安装好 SourceTree 后, 首次运行, 会让你进行一个**初始化设置**, 请**直接跳过**;<br>因为我们已经安装好 Git 客服端了, 如果让 SourceTree 进行初始化, 它会给你重新下载一个 Git 还有 Mercurial.

SourceTree 参考资料:

- [用SourceTree轻巧Git项目图解](http://www.myexception.cn/other/1997033.html)
- [如何在SOURCETREE使用GIT FLOW管理開發!](http://www.takobear.tw/2014/02/15/bear-git-flow-sourcetreegit-flow)

## 10. SourceTree In GitHub
假设已经有了 GitHub 上自己的远程仓库.

### 10.1 创建 SSH Key
首先, 到用户目录下(C:/Users/Administrator), 看看有没有 .ssh 目录, 如果有, 则进入这个目录, 看看有没有 **id\_rsa**(私钥) 和 **id\_rsa.pub**(公钥) 这两个文件, 如果有, 就可以跳过 10.1 后面的操作了, 直接开始 10.2, 如果没有, 则打开 gui bash, 运行下面的命令, 创建一个密钥对:
```
$ ssh-keygen -t rsa -C "youremail@example.com" // 后面的 -C 邮件 是注释, 也可以省略
```

一路回车后, 就在刚才的目录下面生成了 **id\_rsa**(私钥) 和 **id\_rsa.pub**(公钥) 两个文件.

### 10.2 设置 GitHub
登录 GitHub 后, 设置 GitHub 的 "SSH Keys", 就是刚才生成的 **id\_rsa.pub**(公钥) 里面的内容, 直接复制进去就可以了.

设置完后, 可以在 git bash 中执行下面的命令看看有没有设置成功:
```
ssh git@github.com
```

### 10.3 设置 SourceTree
打开 "工具"->"创建/导入SSH密钥", 打开 PuTTY 后, 点击 "Load" 按钮, 选择 9.1 中生成的 **id\_rsa**(私钥) 文件, 然后点击 "Save private key" 按钮, 保存为 **id\_rsa.ppk**.

### 10.4 克隆项目
到你的 GitHub 上拷贝一个项目的 SSH 下来, 打开 SourceTree, 点击 "克隆/新建", 填写好相关信息后, 点击 "克隆", 首次使用, 可能会克隆不下来, SourceTree 会报个错.
```
...
The server's rsa2 key fingerprint is:
ssh-rsa 2048 9f:a7:a4:00:e4:f1:7a:bc:aa:c1:d3:ea:1e:d7:4d:79
If you trust this host, enter "y" to add the key to
PuTTY's cache and carry on connecting.
If you want to carry on connecting just once, without
adding the key to the cache, enter "n".
If you do not trust this host, press Return to abandon the
connection.
```

> 其实这里是让你输 yes / no, 可惜 sourcetree 中输入不了,  得我们手动在 cmd 中操作

这是因为首次连接一个使用 SSH 协议的 Git 服务器的时候, 因为远程 SSH 服务器的公钥没有经过确认而导致 git 命令执行失败, 解决方法是在**命令行**下找到 SourceTree 安装目录里的 PuTTy 工具, 例如 "C:/Program Files (x86)/Atlassian/SourceTree/tools/putty/plink.exe", 然后执行命令
```
plink.exe git@github.com
```

它会继续报刚才的错误, 我们在 cmd 中输入 yes 就可以了.

然后重新在 SourceTree 中克隆项目, 然后它就会提示你, 让你选择私钥 key 文件, 点击桌面右下角任务栏的 "Pageant", 点击 "add key" 按钮, 选择 9.3 中生成的 **id\_rsa.ppk** 即可.

> 每次重新启动电脑后, Pageant 都会清空, 又要重新选择 key 文件 . . . , 等找到解决方法后再来更新笔记.

已经找到解决方法:<br>点击 "工具"->"选项"->"一般", 在 SSH 密钥那一栏里选择我们的 **id\_rsa.ppk**, 就不需要每次都要 "add key" 了.

## 11. git stash 贮藏
git stash 的功能是保存当前工作目录和暂存区, 并 reset --hard HEAD, 即恢复成最新 commit 时的状态.

需要恢复的时候, 使用 **git stash apply** 就将过去贮藏(stashed)的工作区快照恢复了, 和 merge 一样，git stash apply 之前要保证当前目录是干净的(没有未提交的改变), 否则会保错:
```
error: Your local changes to the following files would be overwritten by merge: Please, commit your changes or stash them before you can merge.
```

如果工作区在贮藏之后发生了变化(即更改过 file 并 commit 了), 恢复时就有可能产生冲突(conflict), 这种情况下 git stash apply 会对工作目录进行 merge 操作.

git stash apply 只能恢复工作目录, 如果想把暂存区也按照贮藏时的暂存区恢复的话, 可以加上 **--index** 参数, 如果暂存区恢复时发生冲突了会怎么办呢? 它可不会让你进行 merge, 而是直接报错不允许你这么做:
```
Conflicts in index. Try without --index.
```

所以不要指望让 stash 去恢复暂存区, 如果暂存区有文件, 还是先 commit 的吧, 不然很可能就找不回来了...

在使用 SourceTree 的 stash 功能过程中, 我误解了一点东西, 这里也记录下:

SourceTree 的 stash 操作会让选择一个选项 -- "保存暂存的更改"

![](http://i67.tinypic.com/lees0.jpg)

我一开始以为, 这个选项是控制 --index 参数, 后来发现不管勾不勾这个选项, 恢复时都不会恢复暂存区的内容(估计 SourceTree 也认为不能指望让 stash 去恢复暂存区吧), 那这个选项是干什么的呢?

- 不勾选, 就是正常的 git stash, 和我们使我 git bash 用命令一样的效果
- 勾选, 贮藏后, 工作区的内容被回退到 HEAD, 但保留暂存区里的内容

## 12. 中文显示问题
参考: [http://xstarcd.github.io/wiki/shell/git_chinese.html](http://xstarcd.github.io/wiki/shell/git_chinese.html)

解决 git status 和 push、pull 时, 中文文件名乱码, 如:

```
[/data/soft/xstarcd.github.io]$git status
位于分支 master
您的分支与上游分支 'origin/master' 一致。
未跟踪的文件:
  （使用 "git add <文件>..." 以包含要提交的内容）

        "\346\265\213\350\257\225.txt"

提交为空，但是存在尚未跟踪的文件（使用 "git add" 建立跟踪）
```
<br>
解决方式：

```sh
git config --global core.quotepath false
```
<br>
另外, 还会出现 commit 信息乱码:

```sh
git config --global i18n.commitencoding utf-8
git config --global i18n.logoutputencoding gbk
git config --global gui.encoding utf-8
git config --global svn.pathnameencoding GB2312
```

## 13. 区分文件大小写
默认的 Git 监控是**不区分**文件大小写的 ~

Git 控制是否忽略大小的选项是 `core.ignorecase` (true: 不区分, false: 区分), 但是这个选项在每个 Git 项目的配置里都是默认 true 的, 所以就算配了全局的 `core.ignorecase` 为 `false`, 也会被项目的配置给覆盖掉.

所以一般这里有两种处理方法.

- 第一种, 做全局设置 `git config --global core.ignorecase false`, 并且删除 Git 项目的 `core.ignorecase` 选项
- 第二种, 就是只对当前项目做配置 `git config core.ignorecase false`
