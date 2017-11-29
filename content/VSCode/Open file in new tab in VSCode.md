---
title: Open file in new tab in VSCode
layout: page
date: 2017-11-29 16:58
---

在 VSCode 的资源管理器中打开文件会"冲"掉当前打开的文件, 很不方便.

这是由于默认在 VSCode 的资源管理器中单击文件是预览模式, 双击才是真正的打开文件, 通过用户设置可以改变这种行为.

打开用户设置 (首选项 -> 设置): 

```json
{
    "workbench.editor.enablePreview": false,
    "workbench.editor.enablePreviewFromQuickOpen": false
}
```