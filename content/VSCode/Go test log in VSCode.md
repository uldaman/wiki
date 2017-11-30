---
title: Go test log in VSCode
layout: page
date: 2017-11-30 15:58
---

VSCode 中写 go test 时支持直接运行测试, 但是却输不出 log 信息, 这是因为 VSCode 默认执行 `go test` 时没有 `-v` 参数 (详细输出).

打开用户设置 (首选项 -> 设置): 

```json
{
    "go.testFlags": ["-v"]
}
```