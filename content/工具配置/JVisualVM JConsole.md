---
title: JVisualVM JConsole
layout: page
date: 2016-08-11 10:19
---

# JVisualVM

- JConsole: Java 性能分析器, 用于连接正在运行的 JVM, 不过此 JVM 需要使用可管理的模式启动 (在启动时设置 `com.sun.management.jmxremote`)
- JVisualVM: 升级版的 JConsole, 可以监控线程, 内存情况, 查看方法的 CPU 时间和内存中的对象, 已被 GC 的对象, 反向查看分配的堆栈

**Visual GC** 插件: 监控垃圾回收器

安装:<br>
工具 \-\-\> 插件 \-\-\> 可用插件 \-\-\> 选中 visual gc \-\-\> 安装

![](http://i67.tinypic.com/2iuuvdg.jpg)

具体关于 JVM 内存结构参考:<br>
1. [Java 的内存结构 (Memory Structure) 和垃圾收集 (Garbage Collection) 图解](http://blog.csdn.net/autofei/article/details/7456213)<br>
2. [我对 Java 内存的认识](http://blog.sina.com.cn/s/blog_68158ebf0100wp83.html)<br>
3. [JVM 内存区域划分 Eden Space、Survivor Space、Tenured Gen，Perm Gen 解释](http://blog.chinaunix.net/xmlrpc.php?r=blog/article&uid=29632145&id=4616836)
