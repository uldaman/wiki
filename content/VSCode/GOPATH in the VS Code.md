---
title: GOPATH in the VS Code
layout: page
date: 2017-11-22 16:58
---

打开用户设置 (首选项 -> 设置): 

```json
{
    "go.inferGopath": true,
    "go.toolsGopath": "/Users/hanxiao/Desktop/commonGoPath"
}
```
<br>
**inferGopath** 让编辑器自动推断 Gopath, 规则是沿着当前文件向上找到 src 目录, 并将其设为 Gopath (该结果会覆盖 go.gopath 的设置). 所以我们的项目必须满足 Go 推荐的结构, 即源码要位于 `gopath/src` 目录下.

**toolsGopath** 选项建议设置成全局的一个目录, 它被用来存放一些 VSCode 依赖的 go package, 这样还可以避免项目代码直接引用到这些工具的 src.