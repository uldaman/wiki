---
title: Sublime 配置 Python 开发环境
layout: page
date: 2016-04-24 15:08
---

[TOC]

# 1. 中文文件名方框
这个是 sublime text 3 的 bug, 当 Windows 个性化显示中的设置自定义文本大小 (DPI) 大于默认的 100% 的时候, 就会出现这个bug.

解决方法:

在 sublime text 3 中, Preference, Settings \- User, 最后加上一行 `"dpi_scale": 1.0` 覆盖操作系统设置的 DPI:

```js
{
    "color_scheme": "Packages/Nil-Theme/Sanakan.tmTheme",
    "font_size": 11.0,
    "ignored_packages":
    [
        "Vintage"
    ],
    "update_check": false,
    "word_wrap": "auto",
    "dpi_scale":1.0 // <= 这里
}
```
<br>
# 2. 主题、字体
先安装字体, 我习惯的是 __M+ 1mn regular__

再安装主题, __Monokai Extended__ 和 __Soda Dark__, 使用 package control 就可以安装了.

然后是 sublime 的设置了, 首选项 \-\> 设置 \- 用户

```js
{
    "always_show_minimap_viewport": true,
    "auto_complete_commit_on_tab": false,
    "auto_find_in_selection": true,
    "bold_folder_labels": true,
    "color_scheme": "Packages/Monokai Extended/Monokai Extended.tmTheme",
    "theme": "Soda Dark.sublime-theme",
    "default_line_ending": "unix",
    "detect_indentation": false,
    "detect_slow_plugins": false,
    "dpi_scale": 1.0,
    "drag_text": false,
    "draw_white_space": "selection",
    "ensure_newline_at_eof_on_save": true,
    "fallback_encoding": "UTF-8",
    "font_face": "M+ 1mn regular",
    "font_size": 14,
    "highlight_line": true,
    "highlight_modified_tabs": true,
    "ignored_packages":
    [
        "Vintageous",
        "Vintage"
    ],
    "indent_guide_options":
    [
        "draw_active",
        "draw_normal"
    ],
    "indent_to_bracket": true,
    "line_padding_bottom": 1,
    "line_padding_top": 1,
    "preview_on_click": false,
    "rulers":
    [
        120
    ],
    "scroll_past_end": true,
    "shift_tab_unindent": true,
    "show_encoding": true,
    "tab_size": 4,
    "translate_tabs_to_spaces": true,
    "trim_automatic_white_space": true,
    "trim_trailing_white_space_on_save": true,
    "update_check": false,
    "word_wrap": true,
    "wrap_width": 120
}
```
<br>
保存重启就能看到效果了.

# 3. 代码提示
代码提示插件, 有 __SublimeCodeIntel__、__Anaconda__、__sublimePythonIDE__ 以及 __Jedi__ 等.

不过 winodows 下 __SublimeCodeIntel__、__Anaconda__、__sublimePythonIDE__ 配置有点问题, 总不能达到预期目标, 最终选取了 __Jedi__.

(不过始终觉得 __Anaconda__ 比较好...太可惜了, 装不上.. )

## 3.1 Jedi
Jedi 貌似现在不能通过 Package Control 来安装, 可以直接去 GitHub 上下载 [https://github.com/srusskih/SublimeJEDI](https://github.com/srusskih/SublimeJEDI)

下载好后, 解压文件, 重命名为 Jedi \- Python autocompletion, 然后复制到 Sublime Text 3\\Data\\Packages 目录下.

然后再打开 sublime 插件里就有 Jedi 了.

![](http://i66.tinypic.com/2z4zbjb.jpg)

接下来就是要配置插件的默认设置...

添加按点(dot)\[.\]后出现代码提示.

到 Sublime Text 3\\Data\\Packages\\User 下添加一个文件 Python.sublime-settings (如果没有的话), 添加如下内容:

```js
{
   "auto_complete_triggers": [
        {
            "selector": "source.python",
            "characters": "."
        }
    ],

    "auto_complete_selector": "-",
}
```
<br>
然后, 打开 Jedi 的默认设置文件, 更改下面两条:

![](http://i64.tinypic.com/211v22p.jpg)

```js
"python_interpreter": "D:/Python/python.exe",

"python_package_paths": [
    "D:/Python",
    "D:/Python/DLLs",
    "D:/Python/Lib",
    "D:/Python/Lib/lib-tk",
    "D:/Python/Lib/site-packages"
],
```
<br>
## 3.2 Anaconda
Anaconda 是一个终极 Python 插件, 它为 ST3 增添了多项 IDE 类似的功能，例如:

- Autocompletion: 自动完成，该选项默认开启，同时提供多种配置选项
- Code linting: 使用支持 pep8 标准的 PyLint 或者 PyFlakes
- McCabe code complexity checker: 让你可以在特定的文件中使用 [McCabe complexity checker](http://en.wikipedia.org/wiki/Cyclomatic_complexity). 如果你对软件复杂度检查工具不太熟悉的话，请务必先浏览上边的链接
- Goto Definitions: 能够在你的整个工程中查找并且显示任意一个变量，函数，或者类的定义
- Find Usage: 能够快速的查找某个变量，函数或者类在某个特定文件中的什么地方被使用了
- Show Documentation:  能够显示一个函数或者类的说明性字符串(当然，是在定义了字符串的情况下, 目前只支持英文)

Anaconda 可以通过 Package Control 直接安装.

### 3.2.1 添加按点(dot)\[.\]后出现代码提示.

Python.sublime-settings configuration file in the Packages/User directory (Preferences -> Browse Packages) and add the following:

到 Sublime Text 3\\Data\\Packages\\User (Preferences \-\> Browse Packages \-\> User) 下添加一个文件 Python.sublime-settings (如果没有的话), 添加如下内容:

```js
{
    "auto_complete_triggers": [
        {
            "selector": "source.python - string - comment - constant.numeric",
            "characters": "."
        }
    ]
}
```
<br>
### 3.2.2 PEP8 配置
Preferences \-\> 插件设置 \-\> Anaconda \-\> Settings\-User

```js
{
    "autoformat_ignore":
    [
        "E309",
        "E501"
    ],

    "pep8_ignore":
    [
        "E309",
        "E501" // 忽略长度限制
    ],

    "anaconda_linter_mark_style": "none",

    "anaconda_linter_underlines": false,

    "anaconda_linter_show_errors_on_save": true,
}
```
<br>
### 3.2.3 自动补充函数参数
Preferences \-\> 插件设置 \-\> Anaconda \-\> Settings\-User

```js
{
    "complete_parameters": true, // 输 ( 时补充函数的非默认参数
    "complete_all_parameters": true, // 输 ( 时补充函数的所有参数
}
```
<br>
### 3.2.4 支持 virtualenv
如果想配全局的 virtualenv, Preferences \-\> 插件设置 \-\> Anaconda \-\> Settings\-User:

```js
{
    "python_interpreter": "D:/pydj/project_venv/Scripts/python.exe",
}
```
<br>
如果想为不同的项目配置 virtualenv, 那么修改项目的 \*.sublime-project 文件:

```js
"settings": {
    "python_interpreter": "D:/pydj/project_venv/Scripts/python.exe",
}
```
<br>
# 4. 代码规范检查
如果上一步你能安装 __3.2 Anaconda__ 就不需要这一步了, __Anaconda__ 已经帮你集成好了.

__pylinter__ 插件

这个插件依赖于 python 的 __pylint__ 库, 所以首先, 我们得 `pip install pylint`

安装好后还要对 pylinter 插件做下设置

首选项 \-\> 插件设置 \-\> Pylinter \-\> Settings\-Default

```js
"python_bin": "D:/Python/python.exe",

// The following paths will be added Pylint's Python path
"python_path": [
    "D:/Python",
    "D:/Python/DLLs",
    "D:/Python/Lib",
    "D:/Python/Lib/lib-tk",
    "D:/Python/Lib/site-packages"
],
// Full path to the lint.py module in the pylint package
"pylint_path": "D:/Python/Lib/site-packages/pylint",

// Set to true to automtically run Pylint on save
"run_on_save": true,

// Set to true to use graphical error icons
"use_icons": true,
"disable_outline": true,

// Status messages stay as long as cursor is on an error line
"message_stay": true,
```
<br>
# 5. 自动生成注释
__DocBlockr__ 插件

在 Python 文件中, 文件头、类头以及函数头输入 """ 再按回车即可, 例如:

```
class TestClass():
    """ <-- Press [enter]

def test_function():
    """ <-- Press [enter]
```
<br>
# 6. 项目设置
由於 Python 可以透過 virtualenv 來設定不同的虛擬環境, 透過這些環境來對不同開發專案, 達到環境獨立的優點, 因此我們也會期望在使用 sublime 開發時, 也可以偵測到不同的環境.
首先先用 sublime 创建一个项目, 很简单, 直接 __项目 \-\> 项目另存为__ 就可以了.

保存好后选择 __项目 \-\> 编辑项目__, 设置使用 virtualenv 环境:

```js
{
    "build_systems": [
        {
            "name": "project_venv builder",
            "selector": "source.python",
            "shell_cmd": "D:/pydj/project_venv/bin/python -u \"$file\""
        }
    ],

    "folders": [
        {
          "follow_symlinks": true,
          "path": "."
        }
     ]
}
```
<br>
然后是覆盖 __Jedi__ 默认设置中的 python\_interpreter 和 python\_package\_paths (如果你安装了 __Jedi__ 的话):

```js
{
    "build_systems": [
        {
            "name": "project_venv builder",
            "selector": "source.python",
            "shell_cmd": "D:/pydj/project_venv/Scripts/python -u \"$file\""
        }
    ],

    "folders": [
        {
          "follow_symlinks": true,
          "path": "."
        }
     ],

    "settings": {
        "python_interpreter": "D:/pydj/project_venv/Scripts/python.exe",

        "python_package_paths": [
            "D:/pydj/project_venv/Scripts",
            "D:/pydj/project_venv/Lib",
            "D:/pydj/project_venv/Lib/site-packages"
        ],
    }
}
```
<br>
另外是覆盖 __pylinter__ 的设置 (如果使用的是 __pylinter__ 的话, 另外, virtualenv 里也需要 `pip install pylint`):

```js
{
    "build_systems": [
        {
            "name": "project_venv builder",
            "selector": "source.python",
            "shell_cmd": "D:/pydj/project_venv/Scripts/python -u \"$file\""
        }
    ],

    "folders": [
        {
          "follow_symlinks": true,
          "path": "."
        }
     ],

    "settings": {
        "python_interpreter": "D:/pydj/project_venv/Scripts/python.exe",

        "python_package_paths": [
            "D:/pydj/project_venv/Scripts",
            "D:/pydj/project_venv/Lib",
            "D:/pydj/project_venv/Lib/site-packages"
        ],

        "pylinter": {
            "python_bin": "D:/pydj/project_venv/Scripts/python.exe",

            "python_path": [
                "D:/pydj/project_venv/Scripts",
                "D:/pydj/project_venv/Lib",
                "D:/pydj/project_venv/Lib/site-packages"
            ],
        }
    }
}
```
<br>
# 7. 侧边栏扩展
__SideBarEnhancements__ 插件
安装好几乎不需要设置, 当打开__项目__时, 在项目文件上右键就能显示扩展.

如果安装好 SideBarEnhancements 后没有显示侧边栏, 可以用快捷键 __ctrl + k__, __ctrl + b__ 来打开侧边栏 (不是 ctrl + k + b, 是先按 ctrl + k, 松开再按 ctrl + b), 或者点击菜单: 查看 \-\> 侧边栏 \-\> 显示\\隐藏侧边栏

# 8. 代码调试
__SublimeREPL__ 插件
这个插件允许在编辑界面直接运行 Python 解释器

![](http://i63.tinypic.com/2pzz760.jpg)

## 8.1 SublimeREPL 基础
这部分内容仅做了解, 可直接用 __\[8.2 更好的 SulimeREPL 设置\]__ 替代.

### 8.1.1 添加 virtualenv 支持

打开 SublimeREPL 的用户设置: 首选项 \-\> 插件设置 \-\> SulimeREPL \-\> Setting\-User.

```js
{
    "python_virtualenv_paths": [
        "D:/pydj"
    ]
}
```
<br>
在这里写入 virtualenv 项目的根目录路径, 如我的 virtualenv 项目路径是 `D:/pydj/project_venv`, 所以这里填入 `D:/pdj`

当需要运行 virtualenv 时, 选择 Python \- virtualenv 后, 界面上会出现 virtualenv 选择框:

![](http://i65.tinypic.com/2q2lhg2.jpg)

![](http://i66.tinypic.com/2n7ianq.jpg)

选择后就能打开该 virtualenv 的 python 交互解释器了.

### 8.1.2 以交互式运行当前文件
SublimeREPL 里最常用的应该就是 Python 和 Python \- RUN current file, 前者是打开交互式环境, 后者是直接运行当前文件, 但是一些常见的 python ide 都是把这两者结合起来, SublimeREPL 通过设置也能实现这个功能.

打开 Sublime Text 3\\Packages\\SublimeREPL\\config\\Python\\Main.sublime\-menu 文件 (先备份一份).

__删除__ "id" 为 "repl\_python" 的项(如果想保留也可以不删除), __修改__ "id" 为 "repl\_python\_run" 的项:

```js
[
    {
        "id": "tools",
        "children":[
            {
                "caption": "SublimeREPL",
                "mnemonic": "r",
                "id": "SublimeREPL",
                "children": [
                    {
                        "caption": "Python",
                        "id": "Python",
                        "children": [
                            {
                                "command": "repl_open",
                                "caption": "Python",
                                "id": "repl_python",  // <= 这里
                                "mnemonic": "p",
                                "args": {
                                    "type": "subprocess",
                                    "encoding": "utf8",
                                    "cmd": ["python", "-i", "-u"],
                                    "cwd": "$file_path",
                                    "syntax": "Packages/Python/Python.tmLanguage",
                                    "external_id": "python",
                                    "extend_env": {"PYTHONIOENCODING": "utf-8"}
                                }
                            },
                            {
                                "command": "repl_open",
                                "caption": "Python - RUN current file",
                                "id": "repl_python_run", // <= 这里
                                "mnemonic": "d",
                                "args": {
                                    "type": "subprocess",
                                    "encoding": "utf8",
                                    "cmd": ["python", "-i", "-u", "$file_basename"],
                                    "cwd": "$file_path",
                                    "syntax": "Packages/Python/Python.tmLanguage",
                                    "external_id": "python",
                                    "extend_env": {"PYTHONIOENCODING": "utf-8"}
                                }
                            },
                            {
                                "command": "repl_open",
                                "caption": "Python - PDB current file",
                                "id": "repl_python_pdb",
                                "mnemonic": "d",
                                "args": {
                                    "type": "subprocess",
                                    "encoding": "utf8",
                                    "cmd": ["python", "-i", "-u", "-m", "pdb", "$file_basename"],
                                    "cwd": "$file_path",
                                    "syntax": "Packages/Python/Python.tmLanguage",
                                    "external_id": "python",
                                    "extend_env": {"PYTHONIOENCODING": "utf-8"}
                                }
                            },
                            {
                                "command": "python_virtualenv_repl",
                                "id": "python_virtualenv_repl",
                                "caption": "Python - virtualenv"
                            },
                            {
                                "command": "repl_open",
                                "caption": "Python - IPython",
                                "id": "repl_python_ipython",
                                "mnemonic": "p",
                                "args": {
                                    "type": "subprocess",
                                    "encoding": "utf8",
                                    "autocomplete_server": true,
                                    "cmd": {
                                        "osx": ["python", "-u", "${packages}/SublimeREPL/config/Python/ipy_repl.py"],
                                        "linux": ["python", "-u", "${packages}/SublimeREPL/config/Python/ipy_repl.py"],
                                        "windows": ["python", "-u", "${packages}/SublimeREPL/config/Python/ipy_repl.py"]
                                    },
                                    "cwd": "$file_path",
                                    "syntax": "Packages/Python/Python.tmLanguage",
                                    "external_id": "python",
                                    "extend_env": {
                                        "PYTHONIOENCODING": "utf-8",
                                        "SUBLIMEREPL_EDITOR": "$editor"
                                    }
                                }
                            }
                        ]
                    }
                ]
            }
        ]
    }
]
```
<br>

> 补充下: ipython 是一个 python 的交互式 shell, 比默认的 python shell 好用得多, 支持变量自动补全, 自动缩进, 支持 bash shell 命令, 内置了许多很有用的功能和函数.
不过 SublimeREPL 的 ipython 不怎么好用, 所以一般也不用...

### 8.1.3 关联快捷键
首选项 \-\> 按键绑定 \- 用户

```js
[
    {
        "keys": ["f5"],
        "command": "run_existing_window_command",
        "args": {
            "id": "repl_python_run",
            "file": "config/Python/Main.sublime-menu"
        }
    },
]
```
<br>
## 8.2 更好的 SulimeREPL 设置
在 stackoverflow 上找到一个解决方案 [http://stackoverflow.com/questions/24963030/sublime-text3-and-virtualenvs](http://stackoverflow.com/questions/24963030/sublime-text3-and-virtualenvs)

该方案有很好兼容性, 完全可以替代上面的所有设置, 设置好后, 通过快捷键来触发 SublimeREPL, 并且以后切换 virtualenv 时, 只需要设置项目设置中的 __python_interpreter__ 就可以了.

首选项 \-\> 浏览插件, 创建一个 py 文件: `project_venv_repl.py`

```python
import sublime_plugin
class ProjectVenvReplCommand(sublime_plugin.TextCommand):
    """
    Starts a SublimeREPL, attempting to use project's specified
    python interpreter.
    """
    def run(self, edit, open_file='$file'):
        """Called on project_venv_repl command"""
        cmd_list = [self.get_project_interpreter(), '-i', '-u']
        if open_file:
            cmd_list.append(open_file)
        self.repl_open(cmd_list=cmd_list)

    def get_project_interpreter(self):
        """Return the project's specified python interpreter, if any"""
        settings = self.view.settings()
        return settings.get('python_interpreter', 'python')

    def repl_open(self, cmd_list):
        """Open a SublimeREPL using provided commands"""
        self.view.window().run_command(
            'repl_open', {
                'encoding': 'utf8',
                'type': 'subprocess',
                'cmd': cmd_list,
                'cwd': '$file_path',
                'syntax': 'Packages/Python/Python.tmLanguage'
            }
        )
```
<br>
首选项 \-\> 按键绑定 \- 用户

```js
[
    // Runs currently open file in repl
    {
        "keys": ["f5"],
        "command": "project_venv_repl"
    },
    // Runs repl without any file
    {
        "keys": ["f6"],
        "command": "project_venv_repl",
        "args": {
            "open_file": null
        }
    },
    // Runs a specific file in repl, change main.py to desired file
    {
        "keys": ["f7"],
        "command": "project_venv_repl",
        "args": {
            "open_file": "/home/user/example/main.py"
        }
    }
]
```
<br>

- f5: 以项目设置中的 __python\_interpreter__  来运行当前文件
- f6: 直接运行项目设置中的 __python\_interpreter__
- f7: 以项目设置中的 __python\_interpreter__  来运行指定 py 文件, 更新 open\_file 就可以了.

# 9. 编译 Pyc 文件
## 9.1 生成单个的 pyc 文件
```python
import py_compile
py_compile.compile(r'H:/game/test.py')
```
<br>
执行上面的代码, 就能将 test.py 生成 test.pyc 了.

## 9.2 将整个目录下的 py 文件都生成 pyc
```python
import compileall
compileall.compile_dir(r'H:/game')
```
<br>
执行上面的代码, 就将 game 目录下的所有 py 文件(包含子目录)都生成 pyc 文件了.

当编写项目时, 可以把这个功能做到 __\[工具 \-\> 编译系统\]__ 里或者 __\[SublimeREPL\]__ 里:

```js
{
    "selector": "source.python",
    "shell_cmd": "python -m py_compile \"test.py\""
}

{
    "selector": "source.python",
    "shell_cmd": "python -m compileall \"C:/Users/Administrator/Desktop/Scripts\""
}
```
<br>
