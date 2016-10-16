---
title: 搭建 Hadoop 分布式实验环境
layout: page
date: 2016-10-16 11:42
---

**实验最终成品**:

- 宿主机: win7 64位, 16G
- 虚拟化工具: VMware Workstation
- 虚拟机系统: Ubuntu 32位, 1.5G, 20G, NAT
- 主机名: itcast01 (NameNode), itcast02 (Datanode), itcast03 (Datanode)
- Java 版本: jdk-8u101-linux-i586
- Hadoop 版本: hadoop-2.7.3

关于虚拟机的网络模式解释下, 推荐的是使用**桥接**模式, 因为桥接模式可以让虚拟机和宿主机处于同一网段, 而 **NAT** 模式则是虚拟机单独分配一个网段;<br>然而我实验时发现**桥接**模式虚拟机能 ping 通局域网内所有其他 ip 及外网 ip, 但就是不能和宿主机互 ping, 搞了好久没搞定, 万般无奈选择了 **NAT** 模式.

虚拟机的安装参考另一篇 wiki: [使用 VMware 安装 Ubuntu  、VM Tools 和 Fcitx 输入法](http://wiki.smallcpp.com/%E5%B7%A5%E5%85%B7%E9%85%8D%E7%BD%AE/VMware%20%E5%AE%89%E8%A3%85%20Ubuntu.html)

安装好后虚拟机后, 开始搭建 Hadoop 分布式实验环境.

[TOC]

# 1. 固定 IP、主机名、Hosts 和 iptables
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

![](http://wiki.smallcpp.com/static/images/搭建Hadoop分布式实验环境/ifconfig.png)

另外就是 gateway (网关), 虚拟机的网关可以通过虚拟网络编辑器查看.

![](http://wiki.smallcpp.com/static/images/搭建Hadoop分布式实验环境/虚拟网络编辑器.png)

![](http://wiki.smallcpp.com/static/images/搭建Hadoop分布式实验环境/NAT.png)

![](http://wiki.smallcpp.com/static/images/搭建Hadoop分布式实验环境/gateway.png)

所以上面的第八行的 gateway 要填: `192.168.142.2`.

## DNS
**sudo vim /etc/resolv.conf**, 编辑 DNS 解析文件.

```
nameserver 8.8.8.8
nameserver 8.8.4.4
nameserver 192.168.31.2
```
<br>
第 3 行填 **gateway** (网关) ip;

改完上面, 如果重启的话, DNS 还是会变为原来的样子, 网上给出的方法是执行 `sudo vim /etc/resolvconf/resolv.conf.d/base` 输入和 DNS 解析文件相同的内容.

实际操作后重启发现好像并没生效, 又找到了另一个方法, 执行 `sudo vim /etc/resolvconf/resolv.conf.d/tail` 输入和 DNS 解析文件相同的内容.

最后, 通过 `sudo resolvconf -u` 刷新 **resolv.conf** 文件, 再用 `sudo /etc/init.d/networking restart` 重启网络.

## Hosts
`sudo vim /etc/hostname` 修改主机名为 itcast01 (另外两台分别用 itcast02 和 itcast03).

`sudo vim /etc/hosts` 修改 [ip 域名] 对应表.

```
127.0.0.1       localhost
192.168.31.200  itcast01
```
<br>
## 关闭防火墙
iptables 是 linux 下一个简单实用的防火墙组件.

先用 `sudo ufw status` 查看防火墙状态, 如果是启用的, 就用 `sudo ufw disable` 关闭它.

> Ubuntu 默认不安装 selinux

都设置好后, `reboot` 重启系统, `ifconfig` 查看 ip 是否变为我们设置的, `hostname` 查看主机名.

# 2. 安装 JDK
## 2.1
访问 oracle 官网: [http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

我这里选择的是最新的 `jdk-8u101-linux-i586`.

下载好后, 弄到 Ubuntu 里解压, 然后在 `usr` 目录下新建一个 `java` 目录, 把解压好的文件复制进去.

```
sudo mkdir /usr/java
sudo mv 桌面/jdk1.8.0_101/ /usr/java/
```
<br>
或者可以先 `sudo mkdir /usr/java` 创建好目录, 再用 `sudo tar -zxvf jdk-8u101-linux-i586.tar.gz -C /usr/java` (-z 处理 gzip, x 解压, v 显示详情, f 解压哪个文件) 直接解压到 `/usr/java` 下.

## 2.2
`vim ~/.bashrc` 打开 VIM 编辑器后, 翻到最后一行, 在后面添加:

```
export JAVA_HOME=/usr/java/jdk1.8.0_101
export PATH=$PATH:$JAVA_HOME/bin
```
<br>
注意, "=" 左右两边不能有空格; 最后刷新下文件.

## 2.3
刷新环境变量.

```
source ~/.bashrc
```
<br>
此时, 不管在哪个目录输入 `java -version` 都可以找到执行文件.

![](http://wiki.smallcpp.com/static/images/搭建Hadoop分布式实验环境/javaversion.png)

# 3. 下载安装 Hadoop
访问: [http://archive.apache.org/dist/](http://archive.apache.org/dist/), apache 的所有项目都在这里.

![](http://i61.tinypic.com/29ustjt.jpg)

![](http://i60.tinypic.com/33xy72x.jpg)

![](http://i58.tinypic.com/2nuon4o.jpg)

![](http://wiki.smallcpp.com/static/images/搭建Hadoop分布式实验环境/hadoopdown.png)

下载完成后, 拖到 Ubuntu 桌面.

`sudo mkdir /usr/itcast` 创建一个文件夹.

`cd ~/桌面`, 进入桌面目录.

`sudo tar -zxvf hadoop-2.7.3.tar.gz -C /usr/itcast` (-z 处理 gzip, x 解压, v 显示详情, f 解压哪个文件)

为避免权限问题, 可将 `/usr/itcast/hadoop-2.7.3/` 目录权限改为 **777**: `sudo chmod -R 777 /usr/itcast/hadoop-2.7.3/`

# 4. 配置 Hadoop
`cd /usr/itcast/hadoop-2.7.3/etc/hadoop` 进入 Hadoop 配置文件所在目录.

## 4.1. vim hadoop-env.sh
定位到 26 行左右, 找到

```
export JAVA_HOME=${JAVA_HOME}
```
<br>
改成:

```
export JAVA_HOME=/usr/java/jdk1.8.0_101 (可以在 vim 的命令模式下, 通过 echo $JAVA_HOME 查看路径)
```
<br>
## 4.2. vim core-site.xml
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
        <!--默认为系统目录, 重启会被清空, 导致重启 hadoop 不能用-->
                <name>hadoop.tmp.dir</name>
                <value>/usr/itcast/hadoop-2.7.3/tmp</value>
        </property>
</configuration>
```
## 4.3. vim hdfs-site.xml
```xml
<configuration>
        <property>
        <!--指定 HDFS 保存数据的副本个数, 一般最大为 3 就差不多了-->
                <name>dfs.replication</name>
                <value>2</value>
        </property>
        <property>
    <!--指定元数据保存目录-->
             <name>dfs.namenode.name.dir</name>
             <value>/usr/itcast/hadoop-2.7.3/tmp/dfs/name</value>
        </property>
        <property>
    <!--指定 HDFS 保存数据目录-->
             <name>dfs.datanode.data.dir</name>
             <value>/usr/itcast/hadoop-2.7.3/tmp/dfs/data</value>
        </property>
</configuration>
```
<br>
我们这里有 itcast02 和 itcast03 两台数据节点, 所以 `dfs.replication` 为 2, 如果是伪分布式系统的话, 这里改为 1 就可以了.

## 4.4. vim yarn-env.sh
定位到 23 行左右, 找到 JAVA_HOME, 改为 `export JAVA_HOME=/usr/java/jdk1.8.0_101`

## 4.5. vim yarn-site.xml
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
<br>
## 4.6. mapred-site.xml
```ssh
cp mapred-site.xml.template mapred-site.xml
vim mapred-site.xml
```
<br>

```xml
<configuration>
        <property>
        <!--告诉 Hadoop MR 要运行在 yarn 上-->
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>
        <property>
                <name>mapreduce.jobhistory.address</name>
                <value>itcast01:10020</value>
        </property>
        <property>
                <name>mapreduce.jobhistory.webapp.address</name>
                <value>itcast01:19888</value>
        </property>
</configuration>
```
<br>
## 4.7. vim slaves
如果是伪分布式环境, 不需要配这个环节.

`vim slaves`

打开后去掉第一行的 localhost, 将数据节点的域名添加进来

```
itcast02
itcast03
```
<br>
**注意**, 数据节点的域名要在 Hosts 文件中解析了才行!

## 4.8. 修改环境变量
`vim ~/.bashrc`

```
export JAVA_HOME=/usr/java/jdk1.8.0_101
export HADOOP_HOME=/usr/itcast/hadoop-2.7.3
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin
```
<br>
然后刷新下 bashrc, 命令: `source ~/.bashrc` 退回根目录, 测试下 hadoop 命令: `hadoop version`

![](http://wiki.smallcpp.com/static/images/搭建Hadoop分布式实验环境/hadoopversion.png)

## 4.9. 克隆虚拟机
关闭当前虚拟机后, 从当前虚拟机上克隆两份.

修改克隆出来的虚拟机的**固定 IP****、主机名** 和 **Hosts**.

`sudo vim /etc/network/interfaces` 修改固定 IP

`sudo vim /etc/hostname` 修改主机名.

`sudo vim /etc/hosts` 修改 [ip 域名] 对应表.

```
127.0.0.1       localhost
192.168.31.200  itcast01
192.168.31.201  itcast02
192.168.31.202  itcast03
```
<br>
配好后重启, 在三台虚拟机间互 Ping 测试下.

# 5. 配置 SSH 免密码登录
Ubuntu 默认并没有安装 **ssh** 服务, 需要自己手动安装 **openssh-server**, 可以通过 `ssh localhost` 判断是否安装 ssh 服务; 如果没有安装则通过 `sudo apt-get install openssh-server` 安装即可.

## 5.1 配置 itcast01
在 itcast01 上安装好 **ssh** 服务后.

`cd ~` 进入根目录

`ls -la` 查看下当前目录文件, 可以看到有个隐藏的 `.ssh` 文件夹 (如果没有自己新建个)

`cd .ssh/` 进入 `.ssh` 目录, `ls` 一下, 看看该目录下有没有 `id_rsa`、`id_rsa.pub` 两个文件, 如果没有, 就用 `ssh-keygen -t rsa` 生成一对 (一路回车就好).

`cp id_rsa.pub authorized_keys`

## 5.2 配置 itcast02 和 itcast03
首先也是先安装好 **ssh** 服务生成一对 `id_rsa`、`id_rsa.pub` 文件;

然后**不要**执行 `cp id_rsa.pub authorized_keys`, 而是执行 `ssh-copy-id -i ~/.ssh/id_rsa.pub martin@itcast01` 将公钥追加到 **itcast01** 的 **authorized_keys** 中.

操作好后到 itcast01 中 `vim authorized_keys` 可以看到里面已经多出了 itcast02 和 itcast03 的公钥了.

最后将 **authorized_keys** 远程拷贝到 itcast02 和 itcast03 中.

```
scp authorized_keys martin@itcast02:/home/martin/.ssh/authorized_keys
scp authorized_keys martin@itcast03:/home/martin/.ssh/authorized_keys
```
<br>
#5. 测试环境
hdfs namenode -format

**初始化 HDFS**<br>**命令: hdfs namenode -format**<br>以前是用 hdfs namenode –format, 格式化后, hadoop 根目录下就多出了 tmp 目录(在上一步第二个配置文件里设置的).

**启动 hadoop 服务**
```
cd /usr/itcast/hadoop-2.7.3/sbin/
./start-all.sh
```

输入一堆 yes 和 密码后, 输入 ** jps**, 如果看到 NameNode、ResourceManager、NodeManager、SecondaryNameNode 和 DataNod 六个进程, 就表示启动成功了.

不过有点需要注意, ./start-all.sh 和 hdfs namenode –format 一样, 也是个过时命令, 新的命令是 **start-dfs.sh **和 **start-yarn.sh.**



itcast01:50070 -- hdfs 管理界面

itcast01:8088 -- yarn 管理界面


**先测试 hdfs** -- [http://itcast01:50070](http://itcast01:50070)

![](http://i61.tinypic.com/10fcr2s.jpg)

**hadoop fs -put** /home/itcast/桌面/hadoop-2.7.3.tar.gz hdfs://itcast01:9000/hadoop<br>上传文件到 hdfs://itcast01:9000/ 并命名为 hadoop<br>同样功能的命令除了 put 还有 copyFromLocal (过时).

![](http://i59.tinypic.com/35b9yqr.jpg)

![](http://i58.tinypic.com/x2ns0h.jpg)

**hadoop fs -get** hdfs://itcast01:9000/hadoop /home/itcast/桌面/hadoop.tar.gz<br>下载文件到桌面, 并命名为 hadoop.tar.gz

执行命令的时候, 可能会出现提示: WARN hdfs.DFSClient: DFSInputStream has been closed already
不用管它, apache 也给出了说明:

![](http://i61.tinypic.com/344ql4k.jpg)

**再测试 mr(jar 包) 和 yarn**

MR 给出了一些测试 jar, 它们在: /usr/itcast/hadoop-2.7.3/**share**/hadoop/mapreduce 目录下.

**cd /usr/itcast/hadoop-2.7.3/share/hadoop/mapreduce**

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

/usr/itcast/hadoop-2.7.3/share/hadoop/mapreduce 目录下有个 hadoop-mapreduce-examples-2.7.1.jar, 里面有个 wordcount, 可以用来统计单词个数.
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
