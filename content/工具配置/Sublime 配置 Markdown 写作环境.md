---
title: Sublime 配置 Markdown 写作环境
layout: page
date: 2016-03-05 13:00
---

[TOC]

## 语法高亮
Sublime Text 内置 Markdown 文档的语法高亮, 只是默认的太挫了...

__Monokai Extended__ 和 __Markdown Extended__ 是一套解决方案.

安装完两个插件后, 首先在 \[首选项\] \-\> \[主题方案\] 里设置 *Monokai Extended*, 然后, 打开一个 \*.md 文件, 在 Sublime 右下角显示文件语法那里点击选择 [打开具有当前扩展名的] \-\> [Markdown Extended], 或者在 [查看] \-\> [语法] \-\> [打开具有当前扩展名的] \-\> [Markdown Extended], 这样就将 \*.md 文件与 Markdown Extended 关联起来了.

## 智能补全
__Markdown Editing__ 插件

Markdown Editing 也自带三种配色方案, 不过嘛, 略丑, 还是用 Monokai Extended 和 Markdown Extended 的好.

Markdown Editing 常用功能如下:

- 星号 、 下划线 和 反引号(键盘 1 旁边的那个键) 自动匹配
    + 按 回退键 删除整个符号
    + 按 空格键 删除右边的符号
- 在列表的末尾回车, 下一行自动生成列表符号
    + 在当前列表按 tab 键可生成子列表, 并循环使用 \*, -, + 符号
    + shift tab 键可以反向上面的操作
- 在引用的末尾回车, 下一行自动生成引用符号
- 选取需要的内容后, 按 *>* 能将选取的内容都加上引用
- Ctrl + Win + V 插入链接
- Ctrl + Win + R 插入参考式链接
- Shift + Win + K 插入图片(有冲突, 等下讲怎么处理)
- Ctrl + Shift + B / I 粗体 / 斜体
- Ctrl + 1~6 插入标题

不过我用反引号时, 都是用三个反引号来指定编程语言, 单反引号貌似不能指定语言, 所以改了下 Markdown Editeing 的快捷键, \[首选项\] \-\> \[插件设置\] \-\> \[Markdown Editing\] \-\> \[Key Bindings \-\- User\]

```
    {
        "keys": ["`"], "command": "insert_snippet", "args": {"contents": "```$0\n```\n<br>"}, "context":
        [
             { "key": "setting.auto_match_enabled", "operator": "equal", "operand": true },
             { "key": "selection_empty", "operator": "equal", "operand": true, "match_all": true },
             { "key": "following_text", "operator": "regex_contains", "operand": "^(?:\t| |\\)|]|\\}|$)", "match_all": true },
             { "key": "preceding_text", "operator": "not_regex_contains", "operand": "['a-zA-Z0-9_`]$", "match_all": true },
             { "key": "eol_selector", "operator": "not_equal", "operand": "string.quoted.single", "match_all": true },
             { "key": "selector", "operator": "equal", "operand": "text.html.markdown", "match_all": true }
        ]
    },

    {
        "keys": ["`"], "command": "insert_snippet", "args": {"contents": "```\n${0:$SELECTION}\n```"}, "context":
        [
            { "key": "setting.auto_match_enabled", "operator": "equal", "operand": true },
            { "key": "selection_empty", "operator": "equal", "operand": false, "match_all": true },
            { "key": "selector", "operator": "equal", "operand": "text.html.markdown", "match_all": true }
        ]
    },
```
<br>
另外, __Shift + Win + K__ 插入图片快捷键和输入法有点冲突, 我们改成 __ctrl + super + k__, 一样的打开 [Key Bindings -- User]

```
    {
        "keys": ["ctrl+super+k"], "command": "reference_new_inline_image", "context":
        [
            { "key": "selector", "operator": "equal", "operand": "text.html.markdown", "match_all": true }
        ]
    },
```
<br>
最后, Markdown Editing 默认的粗体和斜体是用下划线控制的, 在 pelican 中转化成貌似有点问题, 所以改成了 \*, \[首选项\] \-\> \[插件设置\] \-\> \[Markdown Editing\] \-\> \[Bold and Italic Settings \-\- User\]:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>name</key>
    <string>Bold and Italic Markers</string>
    <key>scope</key>
    <string>text.html.markdown</string>
    <key>settings</key>
    <dict>
        <key>shellVariables</key>
        <array>
            <dict>
                <key>name</key>
                <string>MD_BOLD_MARKER</string>
                <key>value</key>
                <string>**</string>
            </dict>
            <dict>
                <key>name</key>
                <string>MD_ITALIC_MARKER</string>
                <key>value</key>
                <string>*</string>
            </dict>
        </array>
    </dict>
    <key>uuid</key>
    <string>E3F0F1B0-53C8-11E3-8F96-0800200C9A66</string>
</dict>
</plist>
```
<br>
## 表格辅助
__TableEditor__: Markdown 中的表格书写体验真心不咋样, 所有有人为这个开发了一个插件, 具有较好的自适应性, 会自动对齐.

首先需要用 __ctrl + shift + p__ 打开这个功能:

- Table Editor: Enable for current syntax
- Table Editor: Enable for current view

常用方法:

输入下面的样式


    | Name | Phone |
    |-

按 tab 键

    | Name | Phone |
    |------|-------|
    | _    |       |

使用 __tab__ 切换字段, 按 __enter__ 新建新行

***

输入下面的样式

    |Name|Phone

按 ctrl + k, enter (注意是先按 ctrl + k, 按完后再按 enter)

    | Name | Phone |
    |------|-------|
    | _    |       |

***

行列操作

__alt + shift + up__ 删除当前行

__alt + shift + down__ 在当前行上面新建一行

__alt + up/down__ 上下移动行

__alt + shift + left__ 删除当前列

__alt + shift + right__ 在当前列左边新建列

__alt + right/left__ 左右移动列

## 预览
__OmniMarkupPreviwer__ 可以实时在浏览器中预览

- Ctrl + Alt + O: Preview Markup in Browser.
- Ctrl + Alt + X: Export Markup as HTML.
- Ctrl + Alt + C: Copy Markup as HTML.

还有个 __Markdown Preview__ 插件也是可以预览 md 文件, 但是需要手动生成 + F5 的, 所以感觉用起来比较麻烦.

OmniMarkupPreviwer 的默认配置已经满足日常需求了, 不过可以在配置文件中把 __toc__ 打开, 这样就可以生成目录了.

```
"extensions": ["tables", "strikeout", "fenced_code", "codehilite", "toc"] // 这是 OmniMarkupPreviwer 配置文件的最后一行
```

## 插入文件头
我们使用 Pelican 时, 要在每个 md 文件中添加文件头, 格式大概如下:

```
Title: Git 简明手册
Author: Martin
Date: 2016-03-13 13:00
Summary: Git 学习笔记, 记录一些常用的操作, 以及 SourceTree、Github 的用法.
```

使用 sublime 提供的 code snippet 可以方便的完成添加操作.

"工具"->"新代码片段":

```xml
<snippet>
    <content><![CDATA[
Title: ${1}
Author: Martin
Date: ${2}
Summary:

${3}
]]></content>
    <!-- Optional: Set a tabTrigger to define how to trigger the snippet -->
    <tabTrigger>md</tabTrigger>
    <!-- Optional: Set a scope to limit where the snippet will trigger -->
    <!-- <scope>source.python</scope> -->
</snippet>
```
<br>
保存文件, 取名为 **title-code.sublime-snippet**, 此时, 在文本中, 输入 md, 然后按 tab 键, 就可以直接输入我们的文件头了.

## 插入时间
在我们上一步的操作中, 我们还要手写时间, 这也比较麻烦, sublime 也没有提供直接的方法来插入时间, 不过还好, sublime 可以用插件的方式来扩展功能.

"工具"->"新插件":

```python
import datetime
import sublime, sublime_plugin

class AddCurrentTimeCommand(sublime_plugin.TextCommand):
    def run(self, edit):
        self.view.run_command("insert_snippet",
            {
                "contents": "%s" % datetime.datetime.now().strftime("%Y-%m-%d %H:%M")
            }
        )

```

保存为 __add\_current\_time.py__, 然后打开用户的按键绑定设置, 在后添加一个新快捷键:

```
// 插入时间
{
    "keys": ["ctrl+shift+."], "command":"add_current_time",
},
```
