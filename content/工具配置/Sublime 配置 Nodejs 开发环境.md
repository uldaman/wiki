---
title: Sublime 配置 Nodejs 开发环境
layout: page
date: 2016-10-18 22:19
---

[TOC]

# 语法提示
使用插件: **SublimeText-Nodejs** 插件（[Github](https://github.com/tanepiper/SublimeText-Nodejs)）

**注意**, 这个插件不要用 Sublime 的 Package Control 来安装, Package Control 安装的会没有配置文件导致设置不了插件 . . .

直接在 Github 上下载包下来, 解压重命名为 **Nodejs**, 然后放到 Sublime 的 **Packages** 目录下 (首选项 \-\> 浏览插件);

打开 Nodejs 下面的 `Nodejs.sublime-settings`, 修改里面的 `node_command` 和  `npm_command`, 如下:

```js
{
  // save before running commands
  "save_first": true,
  // if present, use this command instead of plain "node"
  // e.g. "/usr/bin/node" or "C:\bin\node.exe"
  "node_command": "E:\\nodejs\\node.exe",
  // Same for NPM command
  "npm_command": "E:\\nodejs\\npm.cmd",
  // as 'NODE_PATH' environment variable for node runtime
  "node_path": false,

  "expert_mode": false,

  "ouput_to_new_tab": false
}

```
<br>
再打开 ` Nodejs.sublime-build`, 修改里面的 `encoding` 和  `windows cmd`, 如下:

```js
{
  "cmd": ["node", "$file"],
  "file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
  "selector": "source.js",
  "shell": true,
  "encoding": "utf8",
  "windows":
    {
        "cmd": ["taskkill", "/F", "/IM", "node.exe", "&", "node", "$file"]
    },
  "linux":
    {
        "cmd": ["killall node; node", "$file"]
    },
    "osx":
    {
    "cmd": ["killall node; node $file"]
    }
}

```
<br>
配置好后, 打开一个 js 文件使用 `ctrl + b` 就能调用 node.js 来运行脚本了.

# 代码检查
插件: JSHint

这个可以通过 Package Control 安装, 选择插件时, 有两个, 一个是 JSHint, 还有一个是 JSHint Gutter, 推荐使用 JSHint Gutter, 这个版本较新, 可以配置很多另一个 JSHint 没有的功能.

插件设置 \-\> JSHint Gutter \-\> Set Plugin Options, 这里面主要配置 node.js 的路径和 `lint_on_save` (保存时检查, 很好用的功能).

插件设置 \-\> JSHint Gutter \-\> Set Linting Preferences, 这里对 JSHint 的规则做配置, 我这里配好一份, 可参考:

```js
{
  // The plugin looks for a .jshintrc file in the same directory as the source
  // file you're prettifying (or any directory above if it doesn't exist, or in
  // your home folder if everything else fails) and uses those options along
  // the default ones.

  // Details: https://github.com/victorporof/Sublime-JSHint#using-your-own-jshintrc-options
  // Example: https://github.com/jshint/jshint/blob/master/examples/.jshintrc
  // Documentation: http://www.jshint.com/docs/options/
  "globals": {
    "console": true
  },  // 指定没有正式定义的全局变量的白名单
  "browser": true,  // true: 暴露浏览器属性的全局变量, 列如 window, document
  "esnext": true,  // true: 允许 ECMAScript 6 规约, 目前 ES6 的特性不是所有的浏览器都支持
  "unused": true,  // true: 表示禁止变量已经声明, 但却不使用
  "undef": true,  // true: 表示所有的局部变量都必须先声明再使用
  "noempty": true,  // true: 表示禁止出现空的代码块 `{ }`
  "noarg" : false,  // true: 表示禁止使用 `arguments.caller` 和 `arguments.callee`
  "immed ": true,  // true: 立即执行函数必须用括号包起来 `(function () { } ());`
  "eqeqeq": false,  // true: 表示判断相等时, 必须使用 ===
  "strict": "global"  // global: 全局强制使用 ES5 的严格模式 (在每个文件开头 "use strict";), Strict Mode 是对 JS 用法的一些限制, 过滤掉了容易出错的特性和不容易优化的特性
}
```
<br>
最后, 要使用 JSHint 插件, 还需要你的系统安装了 JSHint 包, 可用 npm 直接安装, 网络不好的使用国内镜像安装:

`npm --registry https://registry.npm.taobao.org install jshint`

# 格式化代码
插件: JsFormat

这个没什么好说的, 默认快捷键: `ctrl + alt + f`, 我习惯改成 `ctrl + k + c`.

插件设置 \-\> JsFormat \-\> Key Bindings - Default, 改成:

```js
[
    {
        "keys": ["ctrl+k", "ctrl+c"], "command": "js_format",
        "context": [{"key": "selector", "operator": "equal", "operand": "source.js,source.json"}]
    }
]
```
<br>
# 代码调试
参考另篇 wiki: [NodeJs 调试技巧](http://wiki.smallcpp.com/%E5%B7%A5%E5%85%B7%E9%85%8D%E7%BD%AE/NodeJs%20%E8%B0%83%E8%AF%95%E6%8A%80%E5%B7%A7.html#node-inspector)

# 代码高亮
Sublime 自带了 JS 的高亮, 但有个插件叫 JavaScriptNext, 它对 JS 的渲染更好看些, 可以尝试使用.
