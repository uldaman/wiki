---
title: 折腾 Travis CI
layout: page
date: 2016-05-28 21:04
---

[TOC]

# 介绍
[Travis CI](https://travis-ci.org/) 是在软件开发领域中的一个**在线的**、**分布式**的持续集成服务, 用来构建及测试在 **Github** 托管的代码, 简单的说, 就是它可以监控你 Github 仓库的某个分支, 当你提交修改到这个分支的时候, 它就会把你提交的分支 clone 到它的服务上进行构建, 你可以通过配置来决定构建完成后是否推送回 Github ~

这里记录一些我在将 Simiki 项目和 Travis CI 结合过程中折腾过的坑, 以抛砖引玉...

# 关联 Github 仓库
访问 [Travis CI](https://travis-ci.org/) 官网, 点击页面右上角 **signing\-in button\-signingin** 用 Github 账号登录 Travis CI.

登录成功后, 点击页面左侧 **My Repositories** 旁边的 **+** 号.

![](http://i63.tinypic.com/dndmvt.jpg)

然后选择你要监控的仓库.

![](http://i65.tinypic.com/izcghc.jpg)

最后, 在本地的 Simiki 项目目录中添加一个 `.travis.yml` 文件并创建一个 `.travis` 子目录备用.

# 设置 SSH
## 生成密钥
在前面说过 Travis CI 可以通过配置来决定构建完成后是否推送回 Github, 由于我们需要把 Travis CI 的构建结果, 也就是 wiki 生成文件推送回 Github Project 的 **gh-page** 分支 (参考: [折腾 Simiki](http://wiki.smallcpp.cn/%E5%B7%A5%E5%85%B7%E9%85%8D%E7%BD%AE/%E6%8A%98%E8%85%BE%20Simiki.html)), 所以需要把 Travis CI 的公钥添加到 Simiki 项目的 **Deploy Key** 中 (**注意**, 你添加的是应该是项目的单独 **Deploy Key**, 而不是你 Github 账户的全局 Key).

在本地的 Simiki 项目中生成一对单独密钥: `ssh-keygen -t rsa -C "youremail@example.com"` (因为这是 simiki 项目的单独密钥, 所以不要生成到系统的 .ssh 目录中去了).

把生成的 `id_rsa.pub` 添加到你 Github 仓库上 Simiki 项目的 **Deploy Key** 中, 然后这个公钥就可以删掉了...

## 加密密钥
因为上一步生成的 `id_rsa` 私钥是要发布到 Github 仓库供 Travis CI 拉回服务器的 (Travis CI 需要这个私钥来和 Github 通信), 为了安全, Travis CI 提供了一种加密手段, 也就是说这个私钥只有你的 Travis CI 账户才能解密使用, 就算被别人拿到了也没用, 除非他也拿到了你的 Travis CI 账户.

此时需要使用到 [Travis Client](https://github.com/travis-ci/travis.rb) 了, Travis Client 基于 **Ruby**(>=1.9.3), 所以先检查下你的 Ruby 版本...Windows 用户直接用 [rubyinstaller](http://rubyinstaller.org/) 就好了, ubuntu 用户参考另一 wiki [ubuntu 升级 ruby 版本](http://wiki.smallcpp.cn/%E5%B7%A5%E5%85%B7%E9%85%8D%E7%BD%AE/ubuntu%20%E5%8D%87%E7%BA%A7%20ruby%20%E7%89%88%E6%9C%AC.html)

确认好 Ruby 版本后, 首先要打磨下 **gem** 工具 [打磨 gem](http://wiki.smallcpp.cn/%E5%B7%A5%E5%85%B7%E9%85%8D%E7%BD%AE/%E6%89%93%E7%A3%A8%20gem.html).

然后, 安裝 Travis Client:

```
gem install travis
```
<br>
在安裝完毕, 设置你的登录认证:

```
travis login --auto
```
<br>
会要求输入账号密码, 如此一來, 就能通过 Travis Client 加密 `id_rsa` 了, Travis Client首先会产生一个新的 `id_rsa.enc` 文件, 然后在 `.travis.yml` 的 `before_install` 中插入解密指令.

```
travis encrypt-file id_rsa --add
```
<br>
注意要在你的 Simiki 项目路径执行上面的命令, 正常來說 Travis Client 會自動解析目前的 repo (並把 Private key 上傳到相對應的 repo), 但有時可能會秀逗, 這時你必須在指令後加上 -r 選項來指定 repo 名稱, 例如:

```
travis encrypt-file id_rsa --add -r xxx/xxx
```
<br>

# 配置 .travis.yml

把剛剛製作的 `id_rsa.enc` 移至 `.travis/id_rsa.enc` (`.travis` 是在第一步中新建的子目录), 並在 `.travis` 目录中建立 `ssh_config` 文件, 用来指定 Travis 上的 SSH 設定.

`ssh_config`:

```
Host github.com
  User git
  StrictHostKeyChecking no
  IdentityFile ~/.ssh/id_rsa
  IdentitiesOnly yes
```
<br>
因為剛剛修改了 id_rsa.enc 的位址, 所以我們要順帶修改剛剛 Travis 在 `.travis.yml` 幫我們插入的那條解密指令:

```
- openssl aes-256-cbc -K $encrypted_06b8e90ac19b_key -iv $encrypted_06b8e90ac19b_iv
  -in .travis/id_rsa.enc -out ~/.ssh/id_rsa -d
```
<br>
這條指令會利用 openssl 解密你仓库 `.travis/id_rsa.enc` 文件, 並把解密後的文件放到 Travis 服务器的 `~/.ssh/id_rsa`, 这样 Travis 就能利用这个私钥和你的 Github 通信了.

然后 `.travis.yml` 中还需要配置一些其他选项, 我的 simiki 完整 `.travis.yml` 如下:

```
python:
- '2.7'

before_install:
- openssl aes-256-cbc -K $encrypted_67deb6670456_key -iv $encrypted_67deb6670456_iv
  -in .travis/id_rsa.enc -out ~/.ssh/id_rsa -d
- chmod 600 ~/.ssh/id_rsa
- eval $(ssh-agent)
- ssh-add ~/.ssh/id_rsa
- cp .travis/ssh_config ~/.ssh/config
- git config --global user.name "z351522453"
- git config --global user.email "351522453@qq.com"
- git remote set-url --push origin git@github.com:z351522453/wiki.git  # hack 1
- git fetch origin gh-pages:refs/remotes/origin/gh-pages  # hack 2

install:
- sudo pip install fabric
- sudo pip install ghp-import
- sudo pip install simiki

script:
- simiki g
- fab deploy

branches:
  only:
  - master  # 只监视 master 的提交
```
<br>
大部分都好理解, 重点解释下 **hack 1** 和 **hack 2**.

**hack 1**, 因为 Travis CI 克隆是用的 **http/https** 协议, 所以在推送时就没法使用 SSH 模式了, 于是克隆下来后要改协议为 **ssh/git**.

**hack 2**, Travis CI 克隆项目指定了 \-\-depth, 这样会只拉出当前 master 分支, 如果没有这条配置, 推送时就会报错, 因为这样相当于新建了 gh-pages 分支, 所以必然会提示冲突...加上 hack 2 从 origin 拉回 gh\-pages 分支, 解决下面的错误.

```
To git@github.com:tankywoo/wiki.tankywoo.com.git
 ! [rejected]        gh-pages -> gh-pages (fetch first)
error: failed to push some refs to 'git@github.com:tankywoo/wiki.tankywoo.com.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```
<br>
# Reference
[Travis CI Github](https://github.com/travis-ci/travis.rb)<br>
[Simiki基于Github Pages配合Travis CI做持续集成](https://blog.tankywoo.com/2016/02/19/simiki-with-travis-ci.html)<br>
[使用Travis CI自动部署Hexo](https://xuanwo.org/2015/02/07/Travis-CI-Hexo-Autodeploy/)<br>
[Setup Ruby On Rails on Ubuntu 14.04 Trusty Tahr](https://gorails.com/setup/ubuntu/14.04)<br>
[gem install 出现Errno::ECONNRESET: Connection reset by peer - SSL_connect (https://api.rubygems.org)](http://www.cnblogs.com/ToDoToTry/p/4422454.html)<br>
[修改 gem sources 后遇到的问题](https://ruby-china.org/topics/15260)<br>
