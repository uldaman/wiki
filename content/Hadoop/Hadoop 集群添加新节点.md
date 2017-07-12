---
title: Hadoop 集群添加新节点
layout: page
date: 2016-10-22 14:05
---

仅为**实验**集群或者**小型**集群的添加节点方法, 一般**大型**的集群都有专门的运维.

[TOC]

# 安装系统
首先安装好系统, 配置好 IP、JDK 及 Hadoop, 参考 [搭建 Hadoop 分布式实验环境](http://wiki.smallcpp.cn/Hadoop/%E6%90%AD%E5%BB%BA%20Hadoop%20%E5%88%86%E5%B8%83%E5%BC%8F%E5%AE%9E%E9%AA%8C%E7%8E%AF%E5%A2%83.html) 前三步.

# 设置域名解析
到 **NameNode** 节点下, `cd /usr/smallcpp/hadoop-2.7.3/etc/hadoop` 进入 Hadoop 配置文件所在目录;

`vim slaves` 将新节点的域名添加进来;

`sudo vim /etc/hosts` 修改 [ip 域名] 对应表, 将新节点的 `IP 域名` 添加进来, 然后将**远程拷贝**到新节点中, 如我的新添加的节点主机名是 smallcpp04, 用户是 martin:

`scp /etc/hosts martin@smallcpp04:/home/martin/.ssh/authorized_keys`

同时也将 Hadoop 的配置目录拷贝过去:

`scp /usr/smallcpp/hadoop-2.7.3/etc/hadoop martin@smallcpp04:/usr/smallcpp/hadoop-2.7.3/etc/hadoop`

下面两个操作不知道是不是必须的, 待研究~

- 更新当前集群的 DataNode 的 [ip 域名] 对应表
- 更新当前集群的 DataNode 的 slaves 列表

# 配置 SSH
参考 [搭建 Hadoop 分布式实验环境](http://wiki.smallcpp.cn/Hadoop/%E6%90%AD%E5%BB%BA%20Hadoop%20%E5%88%86%E5%B8%83%E5%BC%8F%E5%AE%9E%E9%AA%8C%E7%8E%AF%E5%A2%83.html#52-smallcpp02-smallcpp03) 第 5.2 步.

# 启动新节点
添加新节点**不需要**重启集群, 新节点会向 NameNode 发送心跳及 blockreport, 集群就知道有新节点上线了, 所以按上面的操作配好后, 到新节点下执行以下操作即可.

```shell
./start-dfs.sh
./start-yarn.sh
./mr-jobhistory-daemon.sh start historyserver
```
<br>
或者:

```shell
./start-all.sh
./mr-jobhistory-daemon.sh start historyserver
```
<br>
执行完毕后, 用 `jps` 看下有没有下面这两个进程:

```
DataNode
NodeManager
```
<br>
# 负载均衡
该步骤可选, 数据负载均衡，目的是为了将其他节点的数据分担一些到新节点上来，比较消耗时间.

```shell
./start-balancer.sh
```
