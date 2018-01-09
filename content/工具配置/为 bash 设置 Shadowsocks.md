---
title: 为 bash 设置 Shadowsocks
layout: page
date: 2017-11-22 16:56
---

[TOC]

Shadowsocks 是常用的代理工具, 它使用 socks5 协议, 而终端很多工具目前只支持 http 和 https 等协议, 对 socks5 协议支持不够好, 所以为终端设置 shadowsocks 的思路就是将 socks 协议转换成 http 协议, 然后为终端设置即可. 可以采用比较知名的 polipo 来实现, polipo 是一个轻量级的缓存 web 代理程序. 

# 安装 polipo
`brew install polipo`

# 设置 parentProxy
`vim /usr/local/opt/polipo/*.plist`

打开 `/usr/local/opt/polipo/homebrew.mxcl.polipo.plist`

在 array 标签组里添加 `<string>socksParentProxy=localhost:1086</string>`, 端口是 Shadowsocks 中设置的 socks5 端口.

# 启动 polipo
`brew services start(/restart) polipo`

# 验证
先用正常模式看下当前网络: `curl ip.gs`.

再用代理模式看下: `http_proxy=http://localhost:8123 curl ip.gs`.

为某个命令设置代理, 前面加上 `http_proxy=http://localhost:8123` 后接命令即可 (该方式对 Git 无效).

> 8123 是 polipo 的默认端口, 如有需要, 可以修改成其他有效端口. 

# 当前会话全局设置
如果嫌每次为每一个命令设置代理比较麻烦, 可以为当前会话设置全局的代理: `export http_proxy=http://localhost:8123`.  如果想撤销当前会话的 http_proxy 代理, 使用 `unset http_proxy`. 

如果想长久设置, 将 export 命令加入 `.bashrc` 或者 `.bash_profile` 文件.

# 设置 Git 代理
使用上面的方式对 Git 无效, Git 走代理需要使用 config.

`git clone xxxx --config http.proxy=localhost:8123`
