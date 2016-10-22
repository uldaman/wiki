---
title: 使用 Eclipse 开发 HDFS
layout: page
date: 2016-10-22 22:08
---

先在 windows 7 下搭建好 eclipse 项目环境, 参考 [Eclipse 配置 Java 开发环境](http://wiki.smallcpp.com/%E5%B7%A5%E5%85%B7%E9%85%8D%E7%BD%AE/Eclipse%20%E9%85%8D%E7%BD%AE%20Java%20%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83.html)

[TOC]

# 导入依赖包
创建一个 Java 工程后, 在工程下新建个 lib 文件夹, 导入 hdfs 的相关 jar 包:

- /usr/itcast/hadoop-2.7.1/share/hadoop/common
- /usr/itcast/hadoop-2.7.1/share/hadoop/common/lib
- /usr/itcast/hadoop-2.7.1/share/hadoop/hdfs
- /usr/itcast/hadoop-2.7.1/share/hadoop/hdfs/lib

测试 hdfs 导入这些目录下的 jar 就可以了…

选中导入后所有的 jar 包, 右键 [构建路径] -> [添加到构建路径]

![](http://i59.tinypic.com/2m43bex.jpg)

# 关联源码
先去 [Hadoop](http://archive.apache.org/dist/) 下载源码包:

![](http://wiki.smallcpp.com/static/images/使用Eclipse开发HDFS/hdfssrc.png)

然后在 Eclipase 上按 `ctrl` 追踪源码时, 就可以设置关联刚下载的 Hadoop 源码了.

![](http://wiki.smallcpp.com/static/images/使用Eclipse开发HDFS/associatedsrc.png)

# 关于权限
当我们远程上传文件时, 可能会报权限不足的错误, 此时有两种处理办法:

**第一种**, 是使用 `FileSystem` 的另一个重载, 传递第三个参数来伪装成集群的 `super-user`, 如:

`FileSystem fs = FileSystem.get(URI.create(uri), new Configuration(), "root");`

**第二种**, 是去 linux 里修改下 hdfs 的配置, 进入 sbin 目录, `vim hdfs-site.xml`

```xml
<property>
    <name>dfs.permissions</name>
    <value>false</value>
</property>
```
<br>
不管哪一种, 都不怎么靠谱, 确实在 Hadoop 1.x 时, 没有怎么考虑安全的问题, 因为 Hadoop 的应用场景是离线数据分析, 一般不对外网开放;<br>
不过在 Hadoop 2.x 中扩展了个 kerberos 项目用来控制 Hadoop 的权限, 如有必要可以再去研究.

> 另外好像还有个 Apache ranger 项目, 也是一个 Hadoop 集群权限框架, 提供操作、监控、管理复杂的数据权限.


