---
title: Shell 命令
layout: page
date: 2016-10-26 11:19
---

[TOC]

# 基础使用
## 执行方式
- 方式一:
    + 需在脚本开头指定解释器: #!/bin/bash
    + 给脚本 +x 权限,
    + 输入脚本绝对路径或相对路径:
        * `/root/test.sh`
        * `./test.sh`
    + 或者在路径前再加个点
        * `. ./test.sh`
    + 区别在于前面的会新开一个 bash (不可见), 也就是在当前 bash 变量不共享, 加个 `.` 表示在当前脚本执行脚本
- 方式二:
    用指定解释器执行 sh test.sh

## 接收控制台输入
`read -p(提示语句) -n(字符个数) -t(等待时间) variable`

## 接收命令返回值
A=\`ls -l\` — 反引号 (飘号)

或者

A=$(ls -l)

## 定义变量
不能有空格, 如果字符串中有空格, 要用引号包围.

- `STR="hello wored"`
- `unset STR` 撤销变量
- `readonly B=2` 只读变量, 不能 unset
- `export` 导出变量为全局环境变量

## 单引号与双引号区别
- 双引号仅转义空格
- 单引号会转义所有特殊字符

## 特殊变量
- `$?` 上一个命令的退出状态
- `$$` 当前进程编号
- `$0` 当前脚本名称
- `$n` 指定位置参数
- `$#` 参数个数
- `$*` / `$@` 参数列表
    + 区别: 当被双引号包围时, `$*` 将参数列表当成一整体, `$@` 会将参数分开

## 整数运算
`expr m + n` (这个命令要有空格)

或者

`$((m+n))`

如计算 (2 + 3) * 4

- 分两步的写法
    + S=\`expr 2 + 3\`
    + `expr $S\*4` (注意 * 要转义)
- 一步的写法
    + echo \`expr \`expr 2 + 3\`\*4\`
    + 或者 `$(((2+3)*4))`

## for 循环
```shell
for N in 1 2 3
do
    echo $N
done
```
<br>
或者

```shell
for N in {1..3}   # 表示 1 到 3
do
    echo $N
done

```
<br>
或者

```shell
for ((i = 0; i <= 5; i++))
do
    echo $i
done
```
<br>
## while 循环
```shell
while expre
do
    echo 'do something'
end
```
<br>
或者

```shell
i=1
while ((i<5))
do
    echo 'do something'
done
```
<br>
## case 选择
```
case $1 in
one)
    ...
    ;;
two)
    ...
    ;;
*)
esac
```
<br>
## if 选择
```shell
if xx
then
    xx
elif xx
then
    xx
else
    xx
fi
```
<br>
## 布尔表达式
`[ xx ]`, 注意 `[  ]` 两边有空格 (非空返回 true)

表达式可使用 `$?` 验证 (0 为 true, >0 为 flase)

`[ xx ] && echo OK`, 条件成立, 执行后面的语句

`[ xx ] && echo OK || echo NO`, 类似三目表达式, 条件成立, echo OK, 不满足执行 echo NO

## 逻辑运算符
- 字符串判断
    + `=` 等于比较
    + `!=` 不等于比较
    + `-z`  空字符串
    + `-n`  非空字符串
- 数值判断
    + `-lt` 小于
    + `-le` 小于等于
    + `-eq` 等于
    + `-gt` 大于
    + `-ge` 大于等于
    + `-ne` 不等于

## 文件判断
- `-r `有读权限
- `-w `有写权限
- `-x `有执行权限
- `-f `文件存在并且是一个常规文件
- `-s `文件存在且不为空
- `-d `文件存在且是一个目录
- `-b `文件存在且是一个块设备
- `-L `文件存在且是一个链接

## 函数
```shell
function xxx() / function start / start()
{
    ...;
    return int; # 只能返回 int 型 1~255
}
```
<br>
函数没有形参, 通过调用时指定, 在函数体内, 通过 `$1~n` 来取.

返回值通过 `$?` 来获取.

```shell
function fSum()
{
    echo $1, $2;
    reutrn $(($1+$2));
}
fSum 3 2;
```
<br>
## 脚本调试
`sh -vx xxx.sh`

或者

在脚本中增加 `set -x`
# sort
sort 命令对 File 参数指定的文件中的**行**排序, 并将结果写到标准输出. 如果 File 参数指定多个文件, 那么 sort 命令将这些文件连接起来, 并当作一个文件进行排序.

```
sort [-fbMnrtuk] [file or stdin]
选项与参数:
-f  : 忽略大小写的差异, 例如 A 与 a 视为编码相同;
-b  : 忽略最前面的空格符部分;
-M  : 以月份的名字来排序, 例如 JAN, DEC 等等的排序方法;
-n  : 使用 "纯数字" 进行排序(默认是以文字型态来排序的);
-r  : 反向排序;
-u  : 就是 uniq, 相同的数据中, 仅出现一行代表;
-t  : 分隔符, 默认是用 [tab] 键来分隔;
-k  : 以那个区间 (field) 来进行排序的意思
```
<br>
对 `/etc/passwd` 的账号进行排序

```
cat /etc/passwd | sort
adm:x:3:4:adm:/var/adm:/sbin/nologin
apache:x:48:48:Apache:/var/www:/sbin/nologin
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
```
<br>
sort 是默认以首字母来排序, 而且默认是以**字符**形式来排序, 所以由字母 a 开始升序排序.

`/etc/passwd` 内容是以 `:` 来分隔的, 我想以第三栏来排序, 该如何? 可以使用 `-t `和 `-k` 参数:

```
cat /etc/passwd | sort -t ':' -k 3
root:x:0:0:root:/root:/bin/bash
uucp:x:10:14:uucp:/var/spool/uucp:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
bin:x:1:1:bin:/bin:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
```
<br>
刚提到 sort 默认是以字符来排序的, 如果想要使用数字排序 (注意后面的 n):

```
cat /etc/passwd | sort -t ':' -k 3n
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/bin/sh
bin:x:2:2:bin:/bin:/bin/sh
```
<br>
默认是升序排序, 如果要倒序排序, 如下 (注意后面的 r):

```
cat /etc/passwd | sort -t ':' -k 3nr
nobody:x:65534:65534:nobody:/nonexistent:/bin/sh
ntp:x:106:113::/home/ntp:/bin/false
messagebus:x:105:109::/var/run/dbus:/bin/false
sshd:x:104:65534::/var/run/sshd:/usr/sbin/nologin
```
<br>
如果要对 `/etc/passwd`, 先以第六个域的第 2 个字符到第 4 个字符进行正向排序, 再基于第一个域进行反向排序:

```
cat /etc/passwd | sort -t ':' -k 6.2,6.4 -k 1r
sync:x:4:65534:sync:/bin:/bin/sync
proxy:x:13:13:proxy:/bin:/bin/sh
bin:x:2:2:bin:/bin:/bin/sh
sys:x:3:3:sys:/dev:/bin/sh
```
<br>
对 `/etc/passwd` 的第七个域进行排序, 然后去重:

```
cat /etc/passwd | sort -t ':' -k 7 -u
root:x:0:0:root:/root:/bin/bash
syslog:x:101:102::/home/syslog:/bin/false
daemon:x:1:1:daemon:/usr/sbin:/bin/sh
sync:x:4:65534:sync:/bin:/bin/sync
sshd:x:104:65534::/var/run/sshd:/usr/sbin/nologin
```
<br>
# uniq
uniq 命令可以去除**排序过**的文件中的重复行, 因此 uniq 经常和 sort 合用.

```
uniq [-icu]
选项与参数:
-i   : 忽略大小写字符的不同;
-c  : 进行计数
-u  : 只显示唯一的行
```
<br>
如存在一个 testfile 文件, 内容如下:

```
hello
world
friend
hello
world
hello
```
<br>
直接删除未经排序的文件, 将会发现没有任何行被删除

```
uniq testfile
hello
world
friend
hello
world
hello
```
<br>
排序文件后再去重

```
cat words | sort |uniq
friend
hello
world
```
<br>
排序之后删除了重复行, 同时在行首位置输出该行重复的次数:

```
sort testfile | uniq -c
1 friend
3 hello
2 world
```
<br>
仅显示存在重复的行, 并在行首显示该行重复的次数:

```
sort testfile | uniq -dc
3 hello
2 world
```
<br>
仅显示不重复的行:

```
sort testfile | uniq -u
friend
```
<br>
# cut
cut 命令可以从一个文本文件或者文本流中提取文本列.

```
选项与参数:
-d  : 后面接分隔字符. 与 -f 一起使用;
-f  : 依据 -d 的分隔字符将一段信息分割成为数段, 用 -f 取出第几段的意思 (即下标);
-c  : 以字符 (characters) 的单位取出固定字符区间;
```
<br>
如 PATH 变量如下 (以 : 分割):

```
echo $PATH
/bin:/usr/bin:/sbin:/usr/sbin:/usr/local/bin:/usr/X11R6/bin:/usr/games
# 1 | 2       | 3   | 4       | 5            | 6            | 7
```
<br>
将 PATH 变量取出, 我要找出第五个路径:

```
echo $PATH | cut -d ':' -f 5
/usr/local/bin
```
<br>
将 PATH 变量取出, 我要找出第三和第五个路径:

```
echo $PATH | cut -d ':' -f 3,5
/sbin:/usr/local/bin
```
<br>
将 PATH 变量取出, 我要找出第三到最后一个路径:

```
echo $PATH | cut -d ':' -f 3-
/sbin:/usr/sbin:/usr/local/bin:/usr/X11R6/bin:/usr/games
```
<br>
将 PATH 变量取出, 我要找出第一到第三个路径:

```
echo $PATH | cut -d ':' -f 1-3
/bin:/usr/bin:/sbin:
```
<br>
将 PATH 变量取出, 我要找出第一到第三, 还有第五个路径:

```
echo $PATH | cut -d ':' -f 1-3,5
/bin:/usr/bin:/sbin:/usr/local/bin
```
<br>
# wc
统计文件里面有多少单词, 多少行, 多少字符.

```
wc [-lLwmc]
选项与参数:
-l  : 仅列出行;
-L  : 最长行数;
-w  : 仅列出多少字 (英文单字);
-m  : 多少字符;
-c  : 统计文件字节;
```
<br>
默认使用 wc 统计 `/etc/passwd`

```
wc /etc/passwd
40   45 1719 /etc/passwd
```
<br>
40 是行数, 45 是单词数, 1719 是字节数.

# sed
sed 是一种在线编辑器, 它一次处理**一行**内容 (即以行为处理单位).

sed 主要用来自动编辑一个或多个文件; 简化对文件的反复操作; 编写转换程序等.

处理时, 把当前处理的行存储在临时缓冲区中, 称为 "模式空间" (pattern space), 接着用 sed 命令处理缓冲区中的内容, 处理完成后, 把缓冲区的内容送往屏幕, 接着处理下一行, 这样不断重复, 直到文件末尾.

seb 默认不会改变源文件内容, 除非你使用重定向存储输出或者 `-i` 参数.

```
sed [-nefr] '[function]'
选项与参数:
-n : 使用安静 (silent) 模式. 默认 seb 会打印所有内容, 安静模式下不打印默认输出 (经常与 p 指令一起用);
-i : 直接修改读取的文件内容, 而不是输出到终端.
-f : 将 sed 的动作写在一个文件内,  -f filename 则可以运行 filename 内的 sed 动作;
-r : sed 的动作支持的是延伸型正规表示法的语法. (默认是基础正规表示法语法)
-e : 多操作, 将多个 seb 操作写成一行;

function (请务必以 ' ' 单引号包围!):
d : 删除;
a : 新增, a 的后面可以接字串, 而这些字串会在新的一行出现 (目前的下一行);
i : 插入, i 的后面可以接字串, 而这些字串会在新的一行出现 (目前的上一行);
s : 替换, s 紧跟的那个字符为分割符 (以字符为单位替换);
c : 替换, c 的后面可以接字串, 这些字串可以取代 n1,n2 之间的行 (以行为单位替换);
p : 指定只打印被修改后的行  (经常与 -n 参数一起用)
q : 完成一个指令后就退出 seb 模式 (如只替换第一个找到的字符)
g : 全局匹配
, : 截取行
```
<br>
将 `/etc/passwd` 的内容列出并且打印行号, 同时, 请将第 2~5 行删除:

> nl 命令用来计算文件中行号

```
nl /etc/passwd | sed '2,5d'
1 root:x:0:0:root:/root:/bin/bash
6 sync:x:5:0:sync:/sbin:/bin/sync
7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
......
```
<br>
要删除第 3 到最后一行:

```
nl /etc/passwd | sed '3,$d'
```
<br>
在第二行后追加 "drink tea" 字样:

```
nl /etc/passwd | sed '2a drink tea'
1 root:x:0:0:root:/root:/bin/bash
2 bin:x:1:1:bin:/bin:/sbin/nologin
drink tea
3 daemon:x:2:2:daemon:/sbin:/sbin/nologin
.....
```
<br>
如果是要增加两行以上, 在第二行后面加入两行字, 例如 "Drink tea or ...." 与 "drink beer?":

```
nl /etc/passwd | sed '2a Drink tea or ......\
> drink beer ?'
1 root:x:0:0:root:/root:/bin/bash
2 bin:x:1:1:bin:/bin:/sbin/nologin
Drink tea or ......
drink beer?
3 daemon:x:2:2:daemon:/sbin:/sbin/nologin
.....
```
<br>
每一行之间都必须要以反斜杠 `\` 来进行新行的添加!

将第 2-5 行的内容取代成为 "No 2-5 number":

```
nl /etc/passwd | sed '2,5c No 2-5 number'
1 root:x:0:0:root:/root:/bin/bash
No 2-5 number
6 sync:x:5:0:sync:/sbin:/bin/sync
.....
```
<br>
通过这个方法我们就能够将数据整行取代了!

仅列出 /etc/passwd 文件内的第 5-7 行:

```
nl /etc/passwd | sed -n '5,7p'
5 lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
6 sync:x:5:0:sync:/sbin:/bin/sync
7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
```
<br>
搜索 `/etc/passwd` 有 root 关键字的行:

```
nl /etc/passwd | sed -n '/root/p'
1  root:x:0:0:root:/root:/bin/bash
....
```
<br>
删除 `/etc/passwd` 所有包含 root 的行, 其他行输出

```
nl /etc/passwd | sed  '/root/d'
2  daemon:x:1:1:daemon:/usr/sbin:/bin/sh
3  bin:x:2:2:bin:/bin:/bin/sh
....
# 第一行的匹配 root 已经删除了
```
<br>
还可以对匹配的结果做进一步处理, 将动作用花括号包围就可以了, 多动作的话用分号分隔, 如搜索 `/etc/passwd`, 将有 root 的行中的 bash 替换为 blueshell:

```
nl /etc/passwd | sed -n '/root/{s/bash/blueshell/;p}'
1  root:x:0:0:root:/root:/bin/blueshell
```
<br>
如果只替换第一个 bash 关键字为 blueshell, 就退出:

```
nl /etc/passwd | sed -n '/bash/{s/bash/blueshell/;p;q}'
1  root:x:0:0:root:/root:/bin/blueshell
```
<br>
s 指令还可以对匹配到的字符串进行替换, s 紧跟的那个字符为分割符, 以下示例用 `/` 分割:

```
sed 's/aaa/bbb/g' 将所有的 aaa 替换成 bbb, 如果不加 g, 只替换每行找到的第一个 aaa
sed 's/aaa/&bbb/' & 表示用找到的字符追加 bbb, 这里就变成了 aaabbb
sed 's#\(love\)able#\1rs', 替换一部分, 括号里的 love 保留并标识为 1, 所以这里 loveable -> 1rs -> lovers
```
<br>
s 指令支持正则表达式.

通过 `-e` 参数可以将多行 sed 命令连写, 如删除 `/etc/passwd` 第三行到末尾的数据, 然后把 bash 替换为 blueshell:

```
nl /etc/passwd | sed -e '3,$d' -e 's/bash/blueshell/'
1  root:x:0:0:root:/root:/bin/blueshell
2  daemon:x:1:1:daemon:/usr/sbin:/bin/sh
```
<br>
# awk
awk 是一个强大的文本分析工具, 相对于 grep 的查找, sed 的编辑, awk 在其对数据分析并生成报告时, 显得尤为强大.

简单来说 awk 就是把文件**逐行**的读入, 以**空格**为默认分隔符将每行切片, 切开的部分再进行各种分析处理.

awk 的强大以至于它甚至形成了自己的语言体系, 即 AWK 编程, 它允许您创建简短的程序, 这些程序读取输入文件、为数据排序、处理数据、对输入执行计算以及生成报表, 还有无数其他的功能. , 但对于我来说, 了解它的基础用法就足够了, 更高级的功能我更喜欢用 Python 来完成.

awk 语言的最基本功能是在文件或者字符串中基于指定规则浏览和抽取信息, awk 抽取信息后, 才能进行其他文本操作.

完整的 awk 脚本通常用来格式化文本文件中的信息, 通常, awk 是以文件的一行为处理单位的. awk 每接收文件的一行, 然后执行相应的命令, 来处理文本.

调用 awk 的三种方式:

```
1. 命令行方式
awk [-F  field-separator]  'commands'  input-file(s)
其中, commands 是真正 awk 命令, 通过包含两部分内容: 'pattern {action}'
pattern : 要查找的内容 (支持正则, 不指定 pattern, 则对所有行进行 action)
action : 对匹配内容所执行的操作 (不指定 action 则默认输出每行的内容)
-F 域分隔符是可选的. input-file(s) 是待处理的文件.
在 awk 中, 文件的每一行中, 由域分隔符分开的每一项称为一个域. 通常, 在不指名 -F 域分隔符的情况下, 默认的域分隔符是空格或 tab.

2. shell 脚本方式
将所有的 awk 命令插入一个文件, 并使 awk 程序可执行, 然后 awk 命令解释器作为脚本的首行, 一遍通过键入脚本名称来调用.
相当于 shell 脚本首行的: #!/bin/sh
可以换成: #!/bin/awk

3. 将所有的 awk 命令插入一个单独文件, 然后调用:
awk -f awk-script-file input-file(s)
其中, -f 选项加载 awk-script-file 中的 awk 脚本, input-file(s) 跟上面的是一样的.
```
<br>
这里着重介绍命令行方式.

假设 `last -n 5` 的输出如下:

```
last -n 5
root     pts/1   192.168.1.100  Tue Feb 10 11:21   still logged in
root     pts/1   192.168.1.100  Tue Feb 10 00:46 - 02:28  (01:41)
root     pts/1   192.168.1.100  Mon Feb  9 11:41 - 18:30  (06:48)
dmtsai   pts/1   192.168.1.100  Mon Feb  9 11:41 - 11:41  (00:00)
root     tty1                   Fri Sep  5 14:09 - 14:10  (00:01)
```
<br>
如果只是显示最近登录的 5 个帐号的名称:

```
last -n 5 | awk  '{print $1}'
root
root
root
dmtsai
root
```
<br>
awk 工作流程是这样的:<br>
读入有 '\\n' 换行符分割的一条记录, 然后将记录按指定的域分隔符划分域, 填充域, `$0` 则表示所有域, `$1` 表示第一个域, `$n` 表示第 n 个域. 默认域分隔符是 "空白键" 或 "[tab]键", 所以 `$1` 表示登录用户, `$3` 表示登录用户ip, 以此类推.

下面的操作只显示 `/etc/passwd` 的账户和账户对应的 shell, 而账户与 shell 之间以 tab 键分割:

```
cat /etc/passwd |awk  -F ':'  '{print $1"\t"$7}'
root    /bin/bash
daemon  /bin/sh
bin     /bin/sh
sys     /bin/sh
```
<br>
下面的操作显示 `/etc/passwd` 的账户和账户对应的 shell, 而账户与 shell 之间以逗号分割, 而且在所有行添加列名 "name, shell", 在最后一行添加 "blue, /bin/nosh".

```
cat /etc/passwd |awk  -F ':'  'BEGIN {print "name,shell"}  {print $1","$7} END {print "blue,/bin/nosh"}'
name,shell
root,/bin/bash
daemon,/bin/sh
bin,/bin/sh
sys,/bin/sh
....
blue,/bin/nosh
```
<br>
awk 工作流程是这样的: 先执行 BEGING, 然后处理文件, 最后执行 END 操作.

搜索 `/etc/passwd` 有 root 关键字的所有行:

```
awk -F ':' '/root/' /etc/passwd
root:x:0:0:root:/root:/bin/bash
```
<br>
搜索 `/etc/passwd` 有 root 关键字的所有行, 并显示对应的 shell:

```
awk -F ':' '/root/{print $7}' /etc/passwd
/bin/bash
```
<br>
awk 有许多内置变量用来设置环境信息, 这些变量可以被改变, 下面给出了最常用的一些变量.

- `ARGC`
    + 命令行参数个数
- `ARGV`
    + 命令行参数排列
- `ENVIRON`
    + 支持队列中系统环境变量的使用
- `FILENAME`
    + awk 浏览的文件名
- `FNR`
    + 浏览文件的记录数
- `FS`
    + 设置输入域分隔符, 等价于命令行 -F选项
- `NF`
    + 浏览记录的域的个数
- `NR`
    + 已读的记录数
- `OFS`
    + 输出域分隔符
- `ORS`
    + 输出记录分隔符
- `RS`
    + 控制记录分隔符
- `$0~n`
    + 域选择器

统计 `/etc/passwd` 文件名, 每行的行号, 每行的列数, 对应的完整行内容:

```
awk  -F ':'  '{print "filename:" FILENAME ", linenumber:" NR ", columns:" NF ", linecontent:"$0}' /etc/passwd
filename:/etc/passwd,linenumber:1,columns:7,linecontent:root:x:0:0:root:/root:/bin/bash
filename:/etc/passwd,linenumber:2,columns:7,linecontent:daemon:x:1:1:daemon:/usr/sbin:/bin/sh
filename:/etc/passwd,linenumber:3,columns:7,linecontent:bin:x:2:2:bin:/bin:/bin/sh
filename:/etc/passwd,linenumber:4,columns:7,linecontent:sys:x:3:3:sys:/dev:/bin/sh
```
<br>
使用 printf 替代 print, 可以让代码更加简洁, 易读:

```
awk  -F ':'  '{printf("filename:%10s, linenumber:%s, columns:%s, linecontent:%s\n",FILENAME, NR, NF, $0)}' /etc/passwd
```
<br>
printf 函数, 其用法和 c 语言中 printf 基本相似, 可以格式化字符串, 输出复杂时, printf 更加好用, 代码更易懂.
