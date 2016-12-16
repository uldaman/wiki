---
title: Hadoop Logs 文件
layout: page
date: 2016-10-20 21:10
---

Hadoop 存在多种日志文件, 其中 **Master** 上的日志文件记录全面信息, 包括 **Slave** 上的 **JobTracker** 与 **DataNode** 也会将错误信息写到 **Master** 中, 而 **Slave** 中的日志主要记录完成的 **Task** 任务信息.

Hadoop Log 文件保存在 Hadoop 根目录下的 logs 目录里.

```shell
martin@smallcpp01:/usr/smallcpp/hadoop-2.7.3/logs$ ls -l
总用量 452
-rw-rw-r-- 1 martin martin 173292 10月 17 01:22 hadoop-martin-namenode-smallcpp01.log
-rw-rw-r-- 1 martin martin   5314 10月 16 17:32 hadoop-martin-namenode-smallcpp01.out
-rw-rw-r-- 1 martin martin   1030 10月 16 17:27 hadoop-martin-namenode-smallcpp01.out.1
-rw-rw-r-- 1 martin martin   1030 10月 16 17:26 hadoop-martin-namenode-smallcpp01.out.2
-rw-rw-r-- 1 martin martin  64382 10月 17 01:22 hadoop-martin-secondarynamenode-smallcpp01.log
-rw-rw-r-- 1 martin martin   1030 10月 16 17:29 hadoop-martin-secondarynamenode-smallcpp01.out
-rw-rw-r-- 1 martin martin   1030 10月 16 17:27 hadoop-martin-secondarynamenode-smallcpp01.out.1
-rw-rw-r-- 1 martin martin  40588 10月 17 01:22 mapred-martin-historyserver-smallcpp01.log
-rw-rw-r-- 1 martin martin   1852 10月 16 20:27 mapred-martin-historyserver-smallcpp01.out
-rw-rw-r-- 1 martin martin      0 10月 16 17:26 SecurityAuth-martin.audit
-rw-rw-r-- 1 martin martin 132156 10月 17 01:22 yarn-martin-resourcemanager-smallcpp01.log
-rw-rw-r-- 1 martin martin   1899 10月 16 17:29 yarn-martin-resourcemanager-smallcpp01.out
-rw-rw-r-- 1 martin martin   1899 10月 16 17:27 yarn-martin-resourcemanager-smallcpp01.out.1
```
<br>
log 文件的名称由几部分构造: **组件** \- **当前用户** \- **节点** \- **主机**

`*.log` 是日志消息, 故障诊断的首要步骤即为检查该文件 (此日志文件最重要)

`*.out` 是当前输出 (记录标准输出和标准错误).
