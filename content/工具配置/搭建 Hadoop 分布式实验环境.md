---
title: 搭建 Hadoop 分布式实验环境
layout: page
date: 2016-10-16 11:42
---

**实验最终成品**:

- 宿主机: win7 64位, 16G
- 虚拟化工具: VMware Workstation
- 虚拟机系统: Ubuntu 32位, 1.5G, 20G, NAT
- Java 版本: jdk-8u101-linux-i586
- Hadoop 版本: hadoop-2.7.3

关于虚拟机的网络模式解释下, 推荐的是使用**桥接**模式, 因为桥接模式可以让虚拟机和宿主机处于同一网段, 而 **NAT** 模式则是虚拟机单独分配一个网段;<br>然而我实验时发现**桥接**模式虚拟机能 ping 通局域网内所有其他 ip 及外网 ip, 但就是不能和宿主机互 ping, 搞了好久没搞定, 万般无奈选择了 **NAT** 模式.

虚拟机的安装参考另一篇 wiki: [使用 VMware 安装 Ubuntu  、VM Tools 和 Fcitx 输入法](http://wiki.smallcpp.com/%E5%B7%A5%E5%85%B7%E9%85%8D%E7%BD%AE/VMware%20%E5%AE%89%E8%A3%85%20Ubuntu.html)

安装好后虚拟机后, 开始搭建 Hadoop 分布式实验环境.

[TOC]

编辑hosts文件
关闭防火墙
安装JDK
下载hadoop 2.x幵解压
修改配置文件
类似linux的方法，配置ssh免密码
分収hadoop到各个节点 (拷贝虚拟机)
部署免密码ssh
格式化namenoede
吭劢hadoop集群
用jps检验各后台迚程是否成功吭劢

# 1. 修改固定 IP 、主机名 及 hosts
## 固定 IP
**sudo vim /etc/network/interfaces**, 编辑 interfaces 文件, 这是 Ubuntu 网上配置文件.

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.31.200
netmask 255.255.255.0
gateway 192.168.31.2
```
<br>
初始文件只有前面两行, 后面的是要添加的内容.

- 第 **1** 行跟第 **4** 行说明 `lo` 接口跟 `eth0` 接口会在系统启动时被自动配置;
- 第 **2** 行将 `lo` 接口设置为一个本地回环 (loopback) 地址;
- 第 **5** 行指出 `eth0` 接口具有一个静态的 (static) IP 配置;
- 第 **6** 行-第 **8** 行分别设置 `eth0` 接口的 ip、掩码和网关.

但是需要注意, 并不所有的都是 `eth0`, 采用的 Ubuntu 版本不同, 也有可能是其他的接口, 如 `ens32`; 可以先使用 `ifconfig` 看下系统使用的是哪个接口.

![](static/images/搭建 Hadoop 分布式实验环境/ifconfig.png)

## DNS
**sudo vim /etc/resolv.conf**, 编辑 DNS 解析文件.
```
nameserver 8.8.8.8
nameserver 8.8.4.4
nameserver 192.168.31.1
```
改完上面, 如果重启的话, DNS 还是会变为原来的样子, 所以要让其永久改变, 因此, 执行: <br>**vim /etc/resolvconf/resolv.conf.d/base**. <br>输入和 DNS 解析文件相同的内容.

**vim /etc/hostname**, 修改主机名.

**vim /etc/hosts**, 修改 [ip 域名] 对应表.
```
127.0.0.1       localhost
192.168.31.200  itcast01
```

#2. 关闭防火墙
iptables 是 linux 下一个简单实用的防火墙组件. <br>**service iptables status**, 先检查下 iptables 状态, 确认是否安装. <br>如果提示: **iptables：unrecognized service**, 则没有安装 iptables, 那就别管它了; <br>如果 iptables 是安装了的, 那么就要查看它的状态, 及是否自启动等等. <br>暂且略过.

都设置好后, **reboot** 重启系统, **ifconfig** 查看 ip 是否变为我们设置的, hostname 查看主机名.

#3. 安装 JDK
访问 oracle 官网 : [http://www.oracle.com/index.html](http://www.oracle.com/index.html)

我们安装的 jdk 1.7 版本, 因为 1.8 版本太新, 和 hadoop 的兼容性不是很好.

![](http://i58.tinypic.com/28gt2bm.jpg)

![](http://i60.tinypic.com/e5lqps.jpg)

**向下翻到最后:**

![](http://i57.tinypic.com/2w69oat.jpg)

![](http://i58.tinypic.com/ny7bck.jpg)

![](http://i60.tinypic.com/i3w7k2.jpg)

![](http://i59.tinypic.com/111896r.jpg)

下载好后, 弄到 Ubuntu 里, 解压, 然后在 usr 目录下新建一个 java 目录, 把解压好的文件复制进去.

**注意, 在接下来的操作前, 需要将用户切回 itcast (命令: sudo itcat)**

```
root@itcast01:/home/itcast# mkdir /usr/java
root@itcast01:/home/itcast# mv 桌面/jdk1.7.0_80/ /usr/java/
vim ~/.bashrc
```

打开 VIM 编辑器后, 翻到最后一行, 在后面添加:

```
export JAVA_HOME=/usr/java/jdk1.7.0_80
export PATH=$PATH:$JAVA_HOME/bin
```
“=”左右两边不能有空格.

```
source ~/.bashrc
```

最后刷新下文件.

此时, 不管在哪个目录输入 java 都可以找到执行文件.

![](http://i62.tinypic.com/4q5sh2.jpg)

#4. 下载安装 Hadoop

访问: [http://archive.apache.org/dist/](http://archive.apache.org/dist/), apache 的所有项目都在这里.

![](http://i61.tinypic.com/29ustjt.jpg)

![](http://i60.tinypic.com/33xy72x.jpg)

![](http://i58.tinypic.com/2nuon4o.jpg)

![](http://i58.tinypic.com/1262ydd.jpg)

下载完成后, 拖到 Ubuntu 桌面.

**mkdir /usr/itcast, **创建一个文件夹.

**cd 桌面**, 进入桌面目录.

**tar -zxvf hadoop-2.7.1.tar.gz -C /usr/itcast** (-z 处理gz, x 释放/c 压缩, v 显示详情, f 解压哪个文件)

接下来开始配置 hadoop, 对于 hadoop 2.0+, **有五个文件需要配置:**

**注意, 在此步操作前, 需要将用户切回 itcast (命令: sudo itcat)**

**linux 默认权限是 644, 需要修改成 777 (命令: sudo chmod -R 777 /usr/itcast/hadoop-2.7.1/)**

**Ubuntu 默认并没有安装 ssh 服务, 需要自己手动安装 openssh-server, 判断是否安装 ssh 服务, 可以通过如下命令进行: ssh localhost.**

**安装命令: sudo apt-get install openssh-server**

* * *

**cd /usr/itcast/hadoop-2.7.1/etc/hadoop**

**vim hadoop-env.sh**<br>定位到 26% 左右, 找到
```
export JAVA_HOME=${JAVA_HOME}
```
改成
```
export JAVA_HOME=/usr/java/jdk1.7.0_80 (可以在 vim 的命令模式下, 通过 echo $JAVA_HOME 查看路径)
```

**vim core-site.xml**
```xml
<configuration>
        <property>
        <!--用来指定 HDFS 的老大(NameNode)的地址-->
                <name>fs.defaultFS</name>
                <!--itcast01 是这台主机名, 要在 hosts 里设置了映射才可以, 不然只能写 ip-->
                <value>hdfs://itcast01:9000</value>
        </property>

        <property>
        <!--用来指定 hadoop 运行时产生文件的存放目录-->
                <name>hadoop.tmp.dir</name>
                <value>/usr/itcast/hadoop-2.7.1/tmp</value>
        </property>
</configuration>
```

**vim hdfs-site.xml**
```xml
<configuration>
        <property>
        <!--指定 HDFS 保存数据的副本个数, 这里因为是伪分布, 所以是 1 份-->
                <name>dfs.replication</name>
                <value>1</value>
        </property>
</configuration>
```

**mv mapred-site.xml.template mapred-site.xml**

**vim mapred-site.xml**
```xml
<configuration>
        <property>
        <!--告诉 Hadoop MR 要运行在 yarn 上-->
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>
</configuration>
```

**vim yarn-site.xml**
```xml
<configuration>
        <property>
            <!--NodeManager 获取数据的方式是shuffle-->
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
        </property>
        <property>
            <!--指定 YARN 的老大(ResourceManager 它负责资源的调度、分配)的地址-->
                <name>yarn.resourcemanager.hostname</name>
                <value>itcast01</value>
        </property>
</configuration>
```

* * *

这五个文件配置好后, 将下来要**修改环境变量**(确保用户是 itcast), 命令: **vim ~/.bashrc

```
export JAVA_HOME=/usr/java/jdk1.7.0_80
export HADOOP_HOME=/usr/itcast/hadoop-2.7.1
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin
```

然后刷新下 bashrc, 命令: **source ~/.bashrc**<br>退回根目录, 测试下 hadoop 命令: hadoop version

**初始化 HDFS**<br>**命令: hdfs namenode -format**<br>以前是用 hdfs namenode –format, 格式化后, hadoop 根目录下就多出了 tmp 目录(在上一步第二个配置文件里设置的).

**启动 hadoop 服务**
```
cd /usr/itcast/hadoop-2.7.1/sbin/
./start-all.sh
```

输入一堆 yes 和 密码后, 输入 ** jps**, 如果看到 NameNode、ResourceManager、NodeManager、SecondaryNameNode 和 DataNod 六个进程, 就表示启动成功了.

不过有点需要注意, ./start-all.sh 和 hdfs namenode –format 一样, 也是个过时命令, 新的命令是 **start-dfs.sh **和 **start-yarn.sh.**

#5. 测试环境

itcast01:50070 -- hdfs 管理界面

itcast01:8088 -- yarn 管理界面


**先测试 hdfs** -- [http://itcast01:50070](http://itcast01:50070)

![](http://i61.tinypic.com/10fcr2s.jpg)

**hadoop fs -put** /home/itcast/桌面/hadoop-2.7.1.tar.gz hdfs://itcast01:9000/hadoop<br>上传文件到 hdfs://itcast01:9000/ 并命名为 hadoop<br>同样功能的命令除了 put 还有 copyFromLocal (过时).

![](http://i59.tinypic.com/35b9yqr.jpg)

![](http://i58.tinypic.com/x2ns0h.jpg)

**hadoop fs -get** hdfs://itcast01:9000/hadoop /home/itcast/桌面/hadoop.tar.gz<br>下载文件到桌面, 并命名为 hadoop.tar.gz

执行命令的时候, 可能会出现提示: WARN hdfs.DFSClient: DFSInputStream has been closed already
不用管它, apache 也给出了说明:

![](http://i61.tinypic.com/344ql4k.jpg)

**再测试 mr(jar 包) 和 yarn**

MR 给出了一些测试 jar, 它们在: /usr/itcast/hadoop-2.7.1/**share**/hadoop/mapreduce 目录下.

**cd /usr/itcast/hadoop-2.7.1/share/hadoop/mapreduce**

创建一个文件, 输入内容

**vim words.txt**
```
hello tom
hello jerry
hello kitty
hello world
hello martin
```

所有的 MR 都是执行在 hdfs 上的, 所以要先上传文件.

**hadoop fs -put words.txt hdfs://itcast01:9000/words.txt**

/usr/itcast/hadoop-2.7.1/share/hadoop/mapreduce 目录下有个 hadoop-mapreduce-examples-2.7.1.jar, 里面有个 wordcount, 可以用来统计单词个数.
**hadoop jar hadoop-mapreduce-examples-2.7.1.jar wordcount hdfs://itcast01:9000/words.txt hdfs://itcast01:9000/result.txt**
第一个参数是待统计文件, 第二个参数是保存结果的文件路径.

执行完毕后, 查看下 hdfs:

**hadoop fs -ls hdfs://itcast01:9000/**
```
Found 4 items
-rw-r--r-- 1 itcast supergroup 210606807 2015-09-19 11:02 hdfs://itcast01:9000/hadoop
drwxr-xr-x - itcast supergroup 0 2015-09-19 11:47 hdfs://itcast01:9000/result.txt
drwx------ - itcast supergroup 0 2015-09-19 11:46 hdfs://itcast01:9000/tmp
-rw-r--r-- 1 itcast supergroup 59 2015-09-19 11:42 hdfs://itcast01:9000/words.txt
```

也可以直接通过 浏览器 查看:

![](http://i60.tinypic.com/117qrh2.jpg)

![](http://i59.tinypic.com/15xo7qa.jpg)

第一个 _SUCCESS 表示执行结果, 这里是成功, 第二个是内容, 把第二个下载下来并打开:
```
hello 5
jerry 1
kitty 1
martin 1
tom 1
world 1
```
