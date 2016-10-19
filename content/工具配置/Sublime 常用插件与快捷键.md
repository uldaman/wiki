---
title: Sublime 常用插件与快捷键
layout: page
date: 2016-10-19 13:27
---

[TOC]

# 常用插件
有个老外推荐了他使用的一些包, 我觉得都很好, 这里也推荐一下 [传送门](https://johnblackbourn.com/my-st3-packages).

我常用的大部分插件在之前的几篇 Wiki 都介绍配置过了.

[Sublime 配置 Markdown 写作环境](http://wiki.smallcpp.com/%E5%B7%A5%E5%85%B7%E9%85%8D%E7%BD%AE/Sublime%20%E9%85%8D%E7%BD%AE%20Markdown%20%E5%86%99%E4%BD%9C%E7%8E%AF%E5%A2%83.html) <br>
[Sublime 配置 Python 开发环境](http://wiki.smallcpp.com/%E5%B7%A5%E5%85%B7%E9%85%8D%E7%BD%AE/Sublime%20%E9%85%8D%E7%BD%AE%20Python%20%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83.html) <br>
[Sublime 配置 Nodejs 开发环境](http://wiki.smallcpp.com/%E5%B7%A5%E5%85%B7%E9%85%8D%E7%BD%AE/Sublime%20%E9%85%8D%E7%BD%AE%20Nodejs%20%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83.html)


以下还有一些其他挺好用的插件也是我常用的.

## UTF8 转换(必备)
插件: [ConvertToUTF8](https://packagecontrol.io/packages/ConvertToUTF8)

这是必备插件, 没什么好说的, 安装就行了.

## 括号高亮
插件: [BracketHighlighter](http://facelessuser.github.io/BracketHighlighter/)

> Bracket Highlighter matches a variety of brackets such as: [], (), {}, "", '', <tag></tag>, and even custom brackets.

BracketHighlighter 插件为 Sublime Text 提供括号、引号这类高亮功能, 但安装此插件后, 默认没有高亮, 只有下划线表示, 而且还不是很醒目, 需要配置:

Preferences \-\> package settings \-\> Bracket Highlighter \-\> Bracket Settings – User, 然后添加如下代码:

```js
{
    "bracket_styles": {
        "default": {
            "icon": "dot",
            "color": "brackethighlighter.default",
            "style": "highlight"
        },

        "unmatched": {
            "icon": "question",
            "color": "brackethighlighter.unmatched",
            "style": "highlight"
        },
        "curly": {
            "icon": "curly_bracket",
            "color": "brackethighlighter.curly",
            "style": "highlight"
        },
        "round": {
            "icon": "round_bracket",
            "color": "brackethighlighter.round",
            "style": "highlight"
        },
        "square": {
            "icon": "square_bracket",
            "color": "brackethighlighter.square",
            "style": "highlight"
        },
        "angle": {
            "icon": "angle_bracket",
            "color": "brackethighlighter.angle",
            "style": "highlight"
        },
        "tag": {
            "icon": "tag",
            "color": "brackethighlighter.tag",
            "style": "highlight"
        },
        "single_quote": {
            "icon": "single_quote",
            "color": "brackethighlighter.quote",
            "style": "highlight"
        },
        "double_quote": {
            "icon": "double_quote",
            "color": "brackethighlighter.quote",
            "style": "highlight"
        },
        "regex": {
            "icon": "regex",
            "color": "brackethighlighter.quote",
            "style": "outline"
        }
    }

}
```
<br>
然后还要针对你的 Sublime 主题进行设置, 如果使用的是默认的 Monokai 主题, 那么主题设置文件在:

- 找到 Sublime text3 安装目录下的 Packages 中的 `Color Scheme – Default.sublime-package`
- 将其重命名为 `Color Scheme – Default.sublime-package.zip`，解压找到 `Monokai.tmTheme`, 拉出来
- `Monokai.tmTheme` 有一堆 `dict` 标签 (注意是有 `key` 子标签的那列 `dict`), 在其同级下添加下面的代码

如果你像我一样, 使用的是 **Monokai Extended** 主题扩展的话, 主题的设置文件在 `Data\Installed Packages\Monokai Extended.sublime-package` 里.

```xml
<dict>
    <key>name</key>
    <string>Bracket Default</string>
    <key>scope</key>
    <string>brackethighlighter.default</string>
    <key>settings</key>
    <dict>
        <key>foreground</key>
        <string>#FFFFFF</string>
        <key>background</key>
        <string>#A6E22E</string>
    </dict>
</dict>

<dict>
    <key>name</key>
    <string>Bracket Unmatched</string>
    <key>scope</key>
    <string>brackethighlighter.unmatched</string>
    <key>settings</key>
    <dict>
        <key>foreground</key>
        <string>#FFFFFF</string>
        <key>background</key>
        <string>#FF0000</string>
    </dict>
</dict>

<dict>
    <key>name</key>
    <string>Bracket Curly</string>
    <key>scope</key>
    <string>brackethighlighter.curly</string>
    <key>settings</key>
    <dict>
        <key>foreground</key>
        <string>#FF00FF</string>
    </dict>
</dict>

<dict>
    <key>name</key>
    <string>Bracket Round</string>
    <key>scope</key>
    <string>brackethighlighter.round</string>
    <key>settings</key>
    <dict>
        <key>foreground</key>
        <string>#E7FF04</string>
    </dict>
</dict>

<dict>
    <key>name</key>
    <string>Bracket Square</string>
    <key>scope</key>
    <string>brackethighlighter.square</string>
    <key>settings</key>
    <dict>
        <key>foreground</key>
        <string>#FE4800</string>
    </dict>
</dict>

<dict>
    <key>name</key>
    <string>Bracket Angle</string>
    <key>scope</key>
    <string>brackethighlighter.angle</string>
    <key>settings</key>
    <dict>
        <key>foreground</key>
        <string>#02F78E</string>
    </dict>
</dict>

<dict>
    <key>name</key>
    <string>Bracket Tag</string>
    <key>scope</key>
    <string>brackethighlighter.tag</string>
    <key>settings</key>
    <dict>
        <key>foreground</key>
        <string>#FFFFFF</string>
        <key>background</key>
        <string>#0080FF</string>
    </dict>
</dict>

<dict>
    <key>name</key>
    <string>Bracket Quote</string>
    <key>scope</key>
    <string>brackethighlighter.quote</string>
    <key>settings</key>
    <dict>
        <key>foreground</key>
        <string>#56FF00</string>
    </dict>
</dict>
```
<br>
配好后的效果如下图:

![](http://wiki.smallcpp.com/static/images/Sublime常用插件与快捷键/BracketHighlighter.png)

## 代码提示、自动补全和链接跳转
**Update**:<br>Forget about *SublimeCodeIntel* and use the new [CodeComplice](https://johnblackbourn.com/sublimecodeintel-st3) package instead.

> Sublime 默认是只提示当前文件, 这里的代码提示指的是全局代码提示.

插件: [SublimeCodeintel](https://packagecontrol.io/packages/SublimeCodeIntel) (已不推荐使用, 见上面的 **Update**).

该插件同时支持**代码提示**、**自动补全**和**链接跳转**三个功能, 非常强大.

另外, 还有一些插件具有单独的功能, 如 [All Autocomplete](https://packagecontrol.io/packages/All%20Autocomplete) 支持全局代码提示, 而 [CTags](https://packagecontrol.io/packages/CTags) 支持链接跳转;<br>
**当然**, 如果你的 SublimeCodeintel 能正常工作, 你是不需要 All Autocomplete 和 CTags 的.

SublimeCodeintel 需要设置一下才能使用, 官网上说要配 `~/.codeintel/config`, 经验证, 这是比较老的方式, 新的已经没有这个文件了 (坑爹啊, 为什么官网不更新!!), 新的配置说明可以参考: [CodeComplice](https://packagecontrol.io/packages/CodeComplice).

首次使用需要配置用户设置后才生效, 选择 `Preferences -> Package Settings -> SublimeCodeIntel -> Setting Default`，将其内容全部复制到 `Setting User` 中;

将 `JavaScript` 为例, 将 `codeintel_language_settings` 的 `JavaScript` 改为下面这样:

```js
"JavaScript": {
    "codeintel_scan_extra_dir": [],
    "codeintel_scan_exclude_dir":["/build/", "/min/"],
    "codeintel_scan_files_in_project": true,
    "codeintel_selected_catalogs": ["jQuery"]
},
```
<br>
## 代码规范检查
插件: [SublimeLinter](http://www.sublimelinter.com/en/latest/)

SublimeLinter 支持全语言, 但不能单独使用, 需要额外下载插件包, 如:

- SublimeLinter-csslint
- SublimeLinter-jshint
- SublimeLinter-contrib-htmlhint
- SublimeLinter-contrib-pylint

同时系统中也需要安装对应的 lint, 如 `npm install -g jshint`.

如果只想使用某个单独语言的代码规范检查, 可以单独安装, 如在 [Sublime 配置 Python 开发环境](http://wiki.smallcpp.com/%E5%B7%A5%E5%85%B7%E9%85%8D%E7%BD%AE/Sublime%20%E9%85%8D%E7%BD%AE%20Python%20%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83.html) 及 [Sublime 配置 Nodejs 开发环境](http://wiki.smallcpp.com/%E5%B7%A5%E5%85%B7%E9%85%8D%E7%BD%AE/Sublime%20%E9%85%8D%E7%BD%AE%20Nodejs%20%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83.html) 中介绍过的 **Pylint** 和 **JSHint**.

## 符号对齐
**Update**:<br>[AlignTab](https://github.com/randy3k/AlignTab): A much more powerful version of the *Alignment* package. Supports regexes and table mode.

AlignTab 预置了一些对齐符号, 如等号, 使用方法: 选中要对齐的代码块, 右键 \-\> `Align By`, 选择按什么符号进行对齐.

同时 AlignTab 还支持自定义符号对齐, 如 `+` 号, 使用方法, 选中要对齐的代码块, `ctrl + shift + p` 呼出命令栏, 输出 `Live`, 选择弹出来的 `Aligntab: Live Preview Mode`, 这是 AlignTab 的**正则**模式, 选择后, Sublime 下方会出现正则输入栏, 在里面输入 '\\+' (因为 + 号是正则表达式, 所以要输入反斜杠转义), 就可以看到效果了.

插件: [Alignment](https://packagecontrol.io/packages/Alignment) (只支持等号对齐, 已不推荐使用, 见上面的 **Update**).

[Alignment 使用详解](https://my.oschina.net/shede333/blog/170536)

## HTML/XML 代码格式化
插件: [Tag](https://github.com/titoBouzout/Tag)

这个插件主要用来进行 HTML/XML 代码格式化, 我一般习惯使用和 JsFormat 相同的快捷键.

插件设置 -> 按键绑定 \- 用户, 添加一行:

```js
{
    "keys": ["ctrl+k", "ctrl+c"],
    "command": "tag_indent_document",
    "context": [{
            "key": "selector",
            "operator": "equal",
            "operand": "text.html,text.htm,text.xml,text.xsl"
        }]
}
```
<br>
## 快速注释
插件: [Doc​Blockr](https://packagecontrol.io/packages/DocBlockr)

这个插件也不需要什么设置, 安装好即可用, 类似 vs 上 tab 键补全注释功能.

## 按模板快速新建文件
插件: [SublimeTmpl](https://packagecontrol.io/packages/SublimeTmpl)

如其名, 无须多解释 . . .

## Emmet
插件: [Emmet](https://sublime.wbond.net/packages/Emmet)

就是以前的 Zen Coding, 方便编写 HTML, 可说是前端必备, 打算单独弄篇 Wiki.

## 文件提示
插件: [Auto​File​Name](https://packagecontrol.io/packages/AutoFileName)

这个插件也是前端用的多, 例如写 CSS 的时候, 如 `background:url(../img/njpg.png) no-repeat;`, 当写到 `../img/` 时插件就被提示这个目录下有哪些文件.

## jQuery 语法提示
插件: [jQuery](https://packagecontrol.io/packages/jQuery)

让 Sublime Text3 支持 jQuery 语法提示, 这个插件不需要设置, 安装后即能使用.

## HTML5 语法提示
插件: [HTML5](https://packagecontrol.io/packages/HTML5)

让 Sublime Text3 支持 HTML5 语法提示, 这个插件不需要设置, 安装后即能使用.

## Sass 语法提示
插件: [Sass](https://sublime.wbond.net/packages/Sass)

让 Sublime Text3 支持 Sass 语法提示, 这个插件不需要设置, 安装后即能使用.

# 常用快捷键

|            命令           |                                 功能                                |
|---------------------------|---------------------------------------------------------------------|
| Ctrl + Shift + P          | 打开命令面板                                                        |
| Ctrl + P                  | 搜索项目中的文件                                                    |
| Ctrl + G                  | 跳转到第几行                                                        |
| Ctrl + R                  | 跳转到 method                                                       |
| Ctrl + W                  | 关闭当前打开文件                                                    |
| Ctrl + D                  | 选择单词, 重复可增加选择下一个相同的单词                            |
| Alt + F3                  | 选择所有相同的单词                                                  |
| Ctrl + L                  | 选择行, 重复可依次增加选择下一行                                    |
| Ctrl + Shift + W          | 关闭所有打开文件                                                    |
| Ctrl + Shift + V          | 粘贴并格式化                                                        |
| Ctrl + Shift + L          | 先选中多行, 再按下快捷键, 会在每行行尾插入光标, 即可同时编辑这些行. |
| Ctrl + Shift + Enter      | 在当前行前插入新行                                                  |
| Ctrl + Shift + D          | 复制整行                                                            |
| Ctrl + X                  | 删除当前行                                                          |
| Ctrl + M                  | 跳转到对应括号                                                      |
| Ctrl + U                  | 软撤销, 撤销光标位置                                                |
| Ctrl + J                  | 选择标签内容                                                        |
| Ctrl + F                  | 查找内容                                                            |
| Ctrl + Shift + F          | 查找并替换                                                          |
| Ctrl + H                  | 替换                                                                |
| Ctrl + N                  | 新建窗口                                                            |
| Ctrl + K + B              | 开关侧栏                                                            |
| Ctrl + Shift + M          | 选中当前括号内容, 重复可选着括号本身                                |
| Ctrl + F2                 | 设置/删除标记                                                       |
| Ctrl + /                  | 注释当前行                                                          |
| Ctrl + Shift + /          | 当前位置插入注释                                                    |
| Ctrl + Alt + /            | 块注释, 并 Focus 到首行, 写注释说明用的                             |
| Ctrl + Shift + A          | (HTML) 选择当前标签前后, 修改标签用的                               |
| F11                       | 全屏                                                                |
| Shift + F11               | 全屏免打扰模式, 只编辑当前文件                                      |
| Alt + F3                  | 选择所有相同的词                                                    |
| Alt + .                   | 闭合标签                                                            |
| Alt + Shift + W           | 使用标签包裹一行                                                    |
| Alt + Shift + 数字        | 分屏显示                                                            |
| Alt + 数字                | 切换打开第N个文件                                                   |
| Shift + 右键拖动          | 光标多行拖动 (和鼠标中键同样功能)                                   |
| 鼠标的前进后退键          | 切换 Tab 文件                                                       |
| 按 Ctrl, 依次点击或选取   | 编辑的多个位置                                                      |
| 按 Ctrl + Shift + 上下键, | 替换行                                                              |

# 全部快捷键

|         命令         |                                   功能                                  |
|----------------------|-------------------------------------------------------------------------|
| **选择类**           |                                                                         |
| Ctrl + D             | 选中光标所占的文本, 继续操作则会选中下一个相同的文本                    |
| Alt + F3             | 选中文本按下快捷键, 即可一次性选择全部的相同文本进行同时编辑.           |
|                      | 举个栗子: 快速选中并更改所有相同的变量名、函数名等.                     |
| Ctrl + L             | 选中整行, 继续操作则继续选择下一行, 效果和 Shift + ↓ 效果一样.          |
| Ctrl + Shift + L     | 先选中多行, 再按下快捷键, 会在每行行尾插入光标, 即可同时编辑这些行.     |
| Ctrl + Shift + M     | 选择括号内的内容 (继续选择父括号).                                      |
|                      | 举个栗子: 快速选中删除函数中的代码, 重写函数体代码或重写括号内里的内容. |
| Ctrl + M             | 光标移动至括号内结束或开始的位置.                                       |
| Ctrl + Enter         | 在下一行插入新行. 即使光标不在行尾, 也能快速向下插入一行.               |
| Ctrl + Shift + Enter | 在上一行插入新行. 即使光标不在行首, 也能快速向上插入一行.               |
| Ctrl + Shift + [     | 选中代码, 按下快捷键, 折叠代码.                                         |
| Ctrl + Shift + ]     | 选中代码, 按下快捷键, 展开代码.                                         |
| Ctrl + K + 0         | 展开所有折叠代码.                                                       |
| Ctrl + ←             | 向左单位性地移动光标, 快速移动光标.                                     |
| Ctrl + →             | 向右单位性地移动光标, 快速移动光标.                                     |
| shift + ↑            | 向上选中多行.                                                           |
| shift + ↓            | 向下选中多行.                                                           |
| Shift + ←            | 向左选中文本.                                                           |
| Shift + →            | 向右选中文本.                                                           |
| Ctrl + Shift + ←     | 向左单位性地选中文本.                                                   |
| Ctrl + Shift + →     | 向右单位性地选中文本.                                                   |
| Ctrl + Shift + ↑     | 将光标所在行和上一行代码互换（将光标所在行插入到上一行之前).            |
| Ctrl + Shift + ↓     | 将光标所在行和下一行代码互换（将光标所在行插入到下一行之后).            |
| Ctrl + Alt + ↑       | 向上添加多行光标, 可同时编辑多行.                                       |
| Ctrl + Alt + ↓       | 向下添加多行光标, 可同时编辑多行.                                       |
| **编辑类**           |                                                                         |
| Ctrl + J             | 合并选中的多行代码为一行. 例如将多行格式的 CSS 属性合并为一行.          |
| Ctrl + Shift + D     | 复制光标所在整行, 插入到下一行.                                         |
| Tab                  | 向右缩进.                                                               |
| Shift + Tab          | 向左缩进.                                                               |
| Ctrl + K + K         | 从光标处开始删除代码至行尾.                                             |
| Ctrl + Shift + K     | 删除整行.                                                               |
| Ctrl + /             | 注释单行.                                                               |
| Ctrl + Shift + /     | 注释多行.                                                               |
| Ctrl + K + U         | 转换大写.                                                               |
| Ctrl + K + L         | 转换小写.                                                               |
| Ctrl + Z             | 撤销.                                                                   |
| Ctrl + Y             | 恢复撤销.                                                               |
| Ctrl + U             | 软撤销, 感觉和 Ctrl + Z 一样.                                           |
| Ctrl + F2            | 设置书签                                                                |
| Ctrl + T             | 左右字母互换.                                                           |
| F6                   | 单词检测拼写                                                            |
| **搜索类**           |                                                                         |
| Ctrl + Shift + P     | 打开 sublime 命令框工具栏.                                              |
| Ctrl + F             | 打开底部搜索框, 查找关键字.                                             |
| Ctrl + shift + F     | 在文件夹内查找, 允许在多个文件夹进行查找.                               |
|                      |                                                                         |
| Ctrl + P             | 打开搜索框. 举个栗子:                                                   |
|                      | 1、输入当前项目中的文件名, 快速搜索文件;                                |
|                      | 2、输入 @ 和关键字, 查找文件中函数名;                                   |
|                      | 3、输入 : 和数字, 跳转到文件中该行代码;                                 |
|                      | 4、输入 # 和关键字, 查找变量名.                                         |
| Ctrl + G             | 打开搜索框, 自动带 :, 输入数字跳转到该行代码.                           |
| Ctrl + R             | 打开搜索框, 自动带 @, 输入关键字, 查找文件中的函数名.                   |
| Ctrl + :             | 打开搜索框, 自动带 #, 输入关键字, 查找文件中的变量名、属性名等.         |
| Esc                  | 退出光标多行选择, 退出搜索框, 命令框等.                                 |
| **显示类**           |                                                                         |
| Ctrl + Tab           | 按文件浏览过的顺序, 切换当前窗口的标签页.                               |
| Ctrl + PageDown      | 向左切换当前窗口的标签页.                                               |
| Ctrl + PageUp        | 向右切换当前窗口的标签页.                                               |
| Alt + Shift + 1      | 窗口分屏, 恢复默认 1 屏（非小键盘的数字)                                |
| Alt + Shift + 2      | 左右分屏 - 2 列                                                         |
| Alt + Shift + 3      | 左右分屏 - 3 列                                                         |
| Alt + Shift + 4      | 左右分屏 - 4 列                                                         |
| Alt + Shift + 5      | 等分 4 屏                                                               |
| Alt + Shift + 8      | 垂直分屏 - 2 屏                                                         |
| Alt + Shift + 9      | 垂直分屏 - 3 屏                                                         |
| Ctrl + K + B         | 开启/关闭侧边栏.                                                        |
| F11                  | 全屏模式                                                                |
| Shift + F11          | 免打扰模式                                                              |
