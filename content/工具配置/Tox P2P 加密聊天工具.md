---
title: Tox P2P 加密聊天工具
layout: page
date: 2017-09-14 16:37
---

[TOC]

Tox 是一个与众不同的即时通讯软件, 最大的特点是 P2P 通讯, 没有服务商的服务器储存各种信息, 所有的一切, 都储存在自己的电脑中, 并且完美兼容 Windows、Linux、MAC、IOS 以及 Android 平台.

这一点带来了利弊两个方面, 好处是这种方式保护了我们的隐私, 避免对话受到外泄和审查, 坏处是一切都储存在你自己的设备上 (电脑、手机), 于是迁移或者多客户端使用就不太方便 (不过可以通过 [Tox聊天(4): qTox 异地同一账号登录](http://www.mintos.org/skill/qtox-switch-devices.html) 曲线救国...).

Tox 是匿名的, 每个 Tox 客户端首次启动时会被分配一个 Tox ID, 这是一个 64 位的 Hash 值, 加 8 位 Nospam 码和 4 位校检码, 一共 76 位.

Tox ID 类似公钥, 你需要将它发给朋友, 以让他们能添加你. 但是好友只能看到你的前 64 位 Hash 值, 后 12 位是可以更换的, 好友看不到.

公布了 Tox ID 后, 时间长了, 可能会受到一些垃圾请求, 那么, 这时候可以更改 Nospam 码, 也即 ID 后的那 8 位, 系统会自动计算出一个 4 位的校检码, 合成新的 12 位值, 与 64 位 Hash ID 组成一个新的 76 位 Tox ID. 这样, 就不能使再使用原先的 Tox ID 添加你了. 这有效的阻止了广告用户, 删除他们, 换一个 Nospam 码, 世界就安静了.

倘若觉得 76 位的 ID 太长太麻烦了, 也可以到一些网站注册一个短用户名, 比如在 utox.org, 写一個用戶名, 填上 Tox ID 值, 注册成功后, 就可以让别人添加 yourname@utox.org 来将你加为好友. 不过如果修改了 Nospam 码, 生成了新的 76 位ID, 就得重新再去注册一个.

但其实官方认为短用户名是没什么意义的. 该软件注重的是隐私, 适合于亲密的人之间交流, 以及不可全信的陌生人之间的交流, 并不是用来聊天交友或者晒照秀隐私的. 并不会频繁添加删除好友, 所以 ID 也并不需要怎么亮眼. 用短 ID 只是为一时的方便而已.

**References**:<br>
[Download](https://tox.chat/download.html)<br>
[Tox聊天(1): Tox 聊天基础篇](http://www.mintos.org/skill/tox-intro.html)<br>
[Tox聊天(2): qTox 聊天实战教程](http://www.mintos.org/skill/qtox-manual.html)<br>
[Tox聊天(3): 安卓版 Antox 上手](http://www.mintos.org/skill/tox-antox-manual.html)<br>
[Tox聊天(4): qTox 异地同一账号登录](http://www.mintos.org/skill/qtox-switch-devices.html)<br>
[Tox聊天(5): Tox 群聊与机器人](http://www.mintos.org/skill/tox-group-robot.html)
