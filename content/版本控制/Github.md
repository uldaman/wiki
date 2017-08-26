---
title: github 常用功能
layout: page
date: 2017-08-26 21:28
---

# watch、star、fork
![](http://wiki.smallcpp.cn/static/images/github/watch.png)

如上图所示, 每个 github 项目的右上角, 都有三个按钮: `watch`、`star`、`fork`.

## watch
这个单词在调试程序时经常遇到, 意为监视, 当把某个变量添加到监视后, 就看实时看到变量的变化.

Github 中的 `watch` 也是类似的功能, 当你监视某个项目后, 如果项目发生变动, 如被别人提交了 pull request、发起了 issue 等等情况, 都会在自己的个人通知中心收到一条通知消息, 如果设置了个人邮箱, 那么还可能收到相应的邮件.

点击 `watch` 按钮后会出现三个选项:

- Not `watch`ing, 不监视, 将不会收到与我无关的通知, 只会收到与我有关的 (我发起的、主动参与的或被 @ 的) 讨论的动态通知
- `watch`ing, 监视, 关注这个项目的所有动态
- Ignoring, 彻底忽略这个项目的动态, 那意味着连与我有关的重要消息也收不到了

通常只会在 `Not `watch`ing` 和 `watch`ing` 中选择.

## star
集`点赞`与`收藏`功能于一体...

点击 github 个人头像, 选择 `your `star`s` 可以查看 `star` 过的列表.

然后就没啥好说的了...

## fork
当对别人的项目进行 `fork` 后, 就会在自己的仓库生成一份源项目的拷贝, 当然这个拷贝只是 `fork` 发生时的项目文件, 如果后续源项目文件发生改变, 必须通过其他的方式去同步. (具体操作看后面的 pull request)

`fork` 应该只在需要对开源项目做贡献时才使用, 很多人会把 `fork` 当成收藏，这是不对的, 收藏应该使用 `star` 功能.

# issue
![](http://wiki.smallcpp.cn/static/images/github/issues.png)

问题列表

# Pull Request
![](http://wiki.smallcpp.cn/static/images/github/newrequest.png)

点击 `New pull request` 按钮创建一个新请求.

![](http://wiki.smallcpp.cn/static/images/github/comparing.png)

该请求是**双向**的:

- 如上图所示的 `base fork` 选择自己的项目而 `head fork` 选择 fork 的源项目时, 创建的请求是从源项目更新到 \-\> 自己的项目
- 反过来, 如果 `base fork` 选择 fork 的源项目而 `head fork` 选择自己的项目时, 创建的请求是把自己项目的更新推送到 \-\> 源项目.
- 如果无法选择 `base fork` 和 `head fork`, 可以点击 `compare across forks` 试试.
