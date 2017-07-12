---
title: NodeJs 调试技巧
layout: page
date: 2016-10-24 18:37
---

[TOC]

# supervisor
node.js 的 supervisor 插件可以帮我们监控文件的改动, 自动重启服务器, 这个 supervisor 是 node.js 的一个包 (而不是 python 的那个 supervisor, 别弄混了), 安装起来很简单, 使用 npm 的安装命令就可以, 因为我们需要在控制台运行, 所以需要安装在全局环境中.

```
npm install -g supervisor
```
<br>
然后就可以使用 supervisor 启动脚本了:

```
supervisor sss.js

Running node-supervisor with
  program 'sss.js'
  --watch '.'
  --extensions 'node,js'
  --exec 'node'

Starting child process with 'node sss.js'
Watching directory 'C:\Users\chaoqun.zhu\Desktop\testjs' for changes.
Press rs for restarting the process.
http://127.0.0.1:5586
crashing child
Starting child process with 'node sss.js'
http://127.0.0.1:5586
crashing child
Starting child process with 'node sss.js'
http://127.0.0.1:5586
crashing child
Starting child process with 'node sss.js'
http://127.0.0.1:5586
```
<br>
# node debug
node.js 提供了一个内建调试器来帮助开发者调试应用程序. 想要开启调试器我们需要在代码中加入 debugger 标签, 当 node.js 执行到 debugger 标签时会自动暂停 (debugger 标签相当于在代码中开启一个断点).

执行命令：`node debug xxx.js` 就可以进入调试模式.

![](http://wiki.smallcpp.cn/static/images/NodeJs调试技巧/nodedebug.png)

以调试模式运行 js 时, 程序会直接断在入口处 (类似 OD 附加程序时), 你可以 cont 一下让程序继续跑起来, 就像 GDB 一样, 可以用 `help` 命令查看自己都可以使用哪些调试命令.

当程序中断时, 可使使用 `repl` 进入程序上下文, 此状态下可以即时求值, 和 Chrome 的 Debug 的 Console 是一样的.

|                   命令                  |               功能              |
|-----------------------------------------|---------------------------------|
| run                                     | 执行脚本, 在入口处会暂停        |
| restart                                 | 重新执行脚本                    |
| cont, c                                 | 继续执行, 直到遇到下一个断点    |
| next, n                                 | 单步执行                        |
| step, s                                 | 单步执行并步入函数              |
| out, o                                  | 从函数中步出                    |
| setBreakpoint(), sb()                   | 当前行设置断点                  |
| setBreakpoint('f()'), sb(...)           | 在函数f的第一行设置断点         |
| setBreakpoint('script.js', 20), sb(...) | 在 script.js 的第 20 行设置断点 |
| clearBreakpoint, cb(...)                | 清除所有断点                    |
| backtrace, bt                           | 显示当前的调用栈                |
| list(5)                                 | 显示当前执行到的前后5行代码     |
| watch(expr)                             | 把表达式 expr 加入监视列表      |
| unwatch(expr)                           | 把表达式 expr 从监视列表移除    |
| watchers                                | 显示监视列表中所有的表达式和值  |
| repl                                    | 在当前上下文打开即时求值环境    |
| kill                                    | 终止当前执行的脚本              |
| scripts                                 | 显示当前已加载的所有脚本        |
| version                                 | 显示 v8 版本                    |

# node-inspector
大部分 node.js 应用都是 web 应用, 所以一些基于 Chrome 的在线调试工具应运而生, 最出名的应该就是 node-inspector 了, 这是一个 node.js 的模块, 安装、使用相当的方便, 首先使用 npm 把其安装在全局环境中.

```
npm install -g node-inspector
```
<br>
node-inspector 是通过 websocket 方式来转向 debug 输入输出的, 因此, 我们在调试前要先启动 node-inspector 来监听 node.js 的 debug 调试端口, 默认情况下 node-inspector 的端口是 8080, 可以通过参数 `--web-port=[port]` 来设置端口.

```
node-inspector --web-port=8888
```
<br>
在启动 node-inpspector 之后, 我们可以通过 `--debug` 或 `--debug-brk` 来启动 node.js 程序.

```
node --debug-brk xxx.js
```
<br>
控制台会返回 "debugger listening on port 5858", 现在打开浏览嚣, 访问 `http://localhost:8888/debug?port=5858`, 这时候就会打开一个很像 Chrome 内置调试工具的界面, 并且代码断点在第一行, 下面就可以使用这个来调试了.

如果你使用 **Sublime** 作为开发工具的话, 可以安装个 [NodeJsDebug](https://github.com/houfeng0923/NodejsDebug) 插件;

安装完以后，打开 `nodejs_debug.sublime-settings` 文件, 将 `chrome-path` 修改成自己电脑上的 Chrome 目录;

然后 `click item 'Nodejs Debug' on contextmenu` 插件就会代替你依次执行启动 node-inspector、启动要调试的 js、用 Chrome 打开调试的 Web 页面三个步骤.

# 使用 Eclipse 调试
Eclipse 提供了一个 node.js 的调试插件, 不过我基本不用 Eclipse, 所以这里仅记录下.
