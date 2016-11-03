author: Martin
date: 2016-01-19 21:03
title: 解决复制出来的虚拟机不能启动 DataNode

今天因测试需要, 需要第二台 hadoop 虚拟机, 于是就从 itcast01 克隆了一份, 命名为 itcast02.

对第二台主机设置了 主机名、静态IP、 HOSTS、hadoop 配置(就是把一些配置文件里的 ip 和 主机名修改下), 重启虚拟机, 然后重新格式化 hdfs, 启动 Hadoop 服务…

JPS 后发现, DataNode 并没有被启动…查资料发现问题原因.

原因是每次 format 都会重新创建一个 id, 而生成的 tmp (这是在配置文件中指定的名字, 可以修改) 目录包含了上次 format 时的 id, format 会清空 namenode 下的数据, 但是并不清空 datanode 下的数据, 导致启动时失败, 所要做的就是每次 fotmat 前, 清空 tmp 下的所有目录.

删除 tmp 文件夹 (`rm -rf tmp`) <br>重新格式化 hdfs (`hdfs namenode -format`) <br>必须要删除 tmp 后再格式化, 不然会启动不了 DataNode.
