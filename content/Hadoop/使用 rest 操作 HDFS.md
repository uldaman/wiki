---
title: 使用 rest 操作 HDFS
layout: page
date: 2016-10-22 22:08
---

[TOC]

要使用 rest 操作 HDFS, 需要先开启集群的 rest 服务.

# vim httpfs-env.sh
```shell
export HTTPFS_HTTP_PORT=14000
```
<br>
# vim core-site.xml
```xml
<property>
    <!--可使用节点-->
    <name>hadoop.proxyuser.root.hosts</name>
    <value>*</value>
</property>
<property>
    <!--可使用用户组-->
    <name>hadoop.proxyuser.root.groups</name>
    <value>*</value>
</property>
```
<br>
# hdfs-site.xml
```xml
<property>
    <!--启动 webhdfs-->
    <name>dfs.webhdfs.enabled</name>
    <value>true</value>
</property>
```
<br>
# 重启 NameNode
修改好配置文件后, 重新启动 NameNode:

```
./stop-all.sh
./start-all.sh
./httpfs.sh start
```
<br>
最后一行就是启动集群的 rest 服务器.

# 测试
执行命令 `curl -i "http://192.168.1.213:14000/webhdfs/v1?user.name=root&op=LISTSTATUS"`

> \-i 表示显示 http response 的头信息, 连同网页代码一起.

- api: `http://<HOST>:<PORT>/webhdfs/v1/<PATH>`
- 参数:
    + user.name 操作者
    + op 操作内容
- 返回 json 格式

更多命令参考 [http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/WebHDFS.html](http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/WebHDFS.html)
