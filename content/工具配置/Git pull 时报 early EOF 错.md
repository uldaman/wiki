---
title: Git pull/clone 时报 early EOF 错
layout: page
date: 2017-07-24 21:28
---

错误信息如下:
fatal: The remote end hung up unexpectedly
fatal: early EOF
fatal: unpack-objects failed

解决方案一:
1. 先启动 git bash
2. 打开一个 cmd, 启动 git 守护进程 `sh.exe --login -i -c 'git.exe daemon --verbose  '`
3. 在 cmd 中标记一些文本, 注意要保持标记状态, 然后回去 git bash 执行 `git reset --hard HEAD` 和 `git pull/clone`

解决方案二:
另外还有一个[解决方案](https://stackoverflow.com/questions/21277806/fatal-early-eof-fatal-index-pack-failed)没试, 因为上面的方案成功了..
