---
title: JDBC 为什么用 Class.forName 加载驱动
layout: page
date: 2016-08-02 16:55
---

[TOC]

JDBC 中注册驱动为什么使用 `Class.forName("com.mysql.jdbc.Driver")` 而不使用 `new com.mysql.jdbc.Driver()`?

其原因有二.

### 原因一
`new` 一个类需要 `import` 它的包, 而各大数据库提供的驱动不一样, 使用 `new` 的方式就十分依赖 `import` 驱动的 jar 包, 这样一旦换了数据库驱动, 还要修改 `import` 语句, 而使用 `Class.forName("com.mysql.jdbc.Driver")` 的话, 可以把 "com.mysql.jdbc.Driver" 写入配置文件, 这样换数据库驱动的话, 只要修改配置文件就行了.


### 原因二
首先, JDBC 规范要求各厂商必须在**静态代码块**中将自己 (Driver) 使用 `DriverManager.registerDriver` 方法注册到 `DriverManager` 中去, 最终使用 `DriverManager` 来操作数据库, 如下图.

![](http://i67.tinypic.com/10fw3no.jpg)

根据 Java 类加载规则, 一个 java 文件从被加载到被卸载这个生命过程, 总共要经历5个阶段:

- **装载**
- **链接**
    + 验证
    + 准备
    + 解析
- **初始化** (对类的静态变量, 静态代码块执行初始化操作)
- **使用** (即实例化)
- **卸载**

> 关于类的加载参考 blog: [018、代码块和类加载](http://blog.smallcpp.com/018-dai-ma-kuai-he-lei-jia-zai.html)

类的加载指的是 **装载** \-\> **链接** \-\> **初始化** 这**三**个阶段, 而使用 `Class.forName()` 方法就是完成类的加载、执行 Driver 中的静态代码块.

`new` 语法实际是和 `Class.forName().newInstances()` 差不多的, 是 **装载** \-\> **链接** \-\> **初始化** \-\> **使用** 这**四**个阶段, 也就是获取一个实例对象, 但我们只需要加载类、注册好驱动就可以了, 并不需要实例化它 (因为并不使用 `Driver` 的实例对象操作数据库, 而是使用 `DriverManager`).
