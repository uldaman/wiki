---
title: nginx + keepalived 高可用高并发架构
layout: page
date: 2016-10-27 22:27
---

[TOC]

nginx + keepalived 的组合通常被用来实现前端高可用.

在这个组合中, 利用 **nginx** 的反向代理实现前端的*动静分离*和*负载均衡*, 如下图.

![](http://wiki.smallcpp.com/static/images/软件工程/nginx.jpg)

在以上的体系中, 我们接着将一些通用的服务从动态 Server 上抽离出来单独封装, 动态 Server 通过 **RPC** 远程调用通用服务, 这样就避免升级通用服务时会更改对所有动态 Server 的问题.

![](http://wiki.smallcpp.com/static/images/软件工程/rpc.jpg)

如上图, 如果 RPC 服务器挂掉了, 那所有的 RPC 请求都会出问题, 为此, 同一个通用服务可以准备多台 RPC 服务器, 通过 **dobbu** 来进行调度, 这样就实现了 RPC 服务器的*高并发*及*高可用*.

![](http://wiki.smallcpp.com/static/images/软件工程/dobbu.jpg)

那么问题又来了, 如果 nginx 宕掉了怎么办?

这时就要让 **keepalived** 出场了, 借助 keepalived 实现 nginx 的*高可用*.

访问网站即访问 IP, keepalived 在这里就是充当着抢这个被访问 IP 的角色, 如下图所示, 我们准备了两台 nginx 服务器, 都装上 keepalived;

![](http://wiki.smallcpp.com/static/images/软件工程/keepalived.jpg)

现在, 我们的网站 IP 被上面那个 keepalived 抢到了, 所以当有 IP 请求时, 是上面的 ngnix 在起作用, 一旦它出问题, 下面那个 keepalived 就会抢到前端网站 IP, 这样就实现了 ngnix 的*高可用*.

在 keepalived 中, 可以通过自定义脚本实现监控任意进程, 一旦被监控进程挂掉, 就能让 keepalived 放弃对 IP 的抢占.

另外, 在数据库层面上, 也有一些优化方案, 如读写分离、缓存系统、分库分表等 ([mongodb](http://www.jianshu.com/p/ddcc3643aec9) 作为一个分布式数据库, 支持自动[分片](http://www.ttlsa.com/mongodb/the-architecture-of-mongodb-mongodb-fragment-cluster-and-simple-construction-scheme/)); 值得一提的是, 现在有个应用叫 **mycat**.

    mycat 能够帮我们实现分库分表的操作, 并且它也支持 mongodb, 开发者可以根据不同的需求将表分配不同的数据节点, 比如 Table A 存放在关系型数据库中 (如 MySQL), 而 Table B 可能更适合 NoSQL (如 MongoDB), 在 MyCAT 中开发者只需要简单配置, 即可让 MyCAT 完成这一系列操作的路由.


如果更进一步, 还可以上 **CDN** 服务, 把静态资源委托给 CDN.

这里也顺便补充下 CDN 的知识:

    CDN 内容分发网络

    CDN 加速将网站的内容缓存在网络边缘 (离用户接入网络最近的地方), 然后在用户访问网站内容的时候, CDN 系统能够实时地根据网络流量和各节点的连接、负载状况以及到用户的距离和响应时间等综合信息将用户的请求重新导向离用户最近的服务节点上.

    CDN 服务最初用于确保快速可靠地分发静态内容, 这些内容可以缓存, 最适合在网速庞大的网络中存储和分发, 该网络在几十多个国家的十几个网络中的覆盖 CDN 网络服务器.

这些知识比较偏硬件, 我认为更多的是 IT、运维或者架构师的职责, 对于目前的我来说, 简单的了解下, 知道有这些个东西就足够了, 更多的参考资料可参考:

**Refrences**:<br>
[Linux 高可用 (HA) 集群基本概念详解](http://freeloda.blog.51cto.com/2033581/1265304)<br>
[Linux 高可用 (HA) 集群之 Heartbeat 详解](http://freeloda.blog.51cto.com/2033581/1265808)<br>
[Linux 高可用 (HA) 集群之 heartbeat 基于 crm 进行资源管理详解](http://freeloda.blog.51cto.com/2033581/1270239)<br>
[Linux 高可用 (HA) 集群之 Corosync 详解](http://freeloda.blog.51cto.com/2033581/1272417)<br>
[Linux 高可用 (HA) 集群之 Pacemaker 详解](http://freeloda.blog.51cto.com/2033581/1274533)<br>
[Linux 高可用 (HA) 集群之 DRB 详解](http://freeloda.blog.51cto.com/2033581/1275384)<br>
[Linux 高可用 (HA) 集群之 keepalived 详解](http://freeloda.blog.51cto.com/2033581/1280962)<br>
[Nginx + Keepalived 实现站点高可用](http://www.codes51.com/article/detail_573458_1.html)
