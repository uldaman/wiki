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

另外就是 **gateway** (网关), 虚拟机的网关可以通过虚拟网络编辑器查看.

![](http://wiki.smallcpp.com/static/images/搭建Hadoop分布式实验环境/虚拟网络编辑器.png)

![](http://wiki.smallcpp.com/static/images/搭建Hadoop分布式实验环境/NAT.png)

![](http://wiki.smallcpp.com/static/images/搭建Hadoop分布式实验环境/gateway.png)

所以上面的第八行的 **gateway** 要填: `192.168.142.2`.

## DNS
**sudo vim /etc/resolv.conf**, 编辑 DNS 解析文件.

```
nameserver 8.8.8.8
nameserver 8.8.4.4
nameserver 192.168.31.2
```
<br>
第 3 行填 **gateway** (网关) ip;

改完上面, 如果重启的话, DNS 还是会变为原来的样子;

网上给出的方法是执行 `sudo vim /etc/resolvconf/resolv.conf.d/base` 输入和 DNS 解析文件相同的内容.

实际操作后重启发现好像并没生效, 又找到了另一个方法, 执行 `sudo vim /etc/resolvconf/resolv.conf.d/tail` 输入和 DNS 解析文件相同的内容.

最后, 通过 `sudo resolvconf -u` 刷新 **resolv.conf** 文件, 再用 `sudo /etc/init.d/networking restart` 重启网络.

## Hosts
`sudo vim /etc/hostname` 修改主机名为 itcast01 (另外两台分别用 itcast02 和 itcast03).

`sudo vim /etc/hosts` 修改 [ip 域名] 对应表.

```
127.0.0.1       localhost
192.168.31.200  itcast01
192.168.31.201  itcast02
192.168.31.201  itcast03
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
修改环境变量.

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

# 3. 安装 Hadoop
## 3.1
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

## 3.2
修改环境变量.

`vim ~/.bashrc`

```
export JAVA_HOME=/usr/java/jdk1.8.0_101
export HADOOP_HOME=/usr/itcast/hadoop-2.7.3
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```
<br>
## 3.3
刷新环境变量.

`source ~/.bashrc` 退回根目录, 测试下 hadoop 命令: `hadoop version`

![](http://wiki.smallcpp.com/static/images/搭建Hadoop分布式实验环境/hadoopversion.png)

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
```shell
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

## 4.8. 克隆虚拟机
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

## 5.1. 配置 itcast01
在 itcast01 上安装好 **ssh** 服务后.

`cd ~` 进入根目录

`ls -la` 查看下当前目录文件, 可以看到有个隐藏的 `.ssh` 文件夹 (如果没有自己新建个)

`cd .ssh/` 进入 `.ssh` 目录, `ls` 一下, 看看该目录下有没有 `id_rsa`、`id_rsa.pub` 两个文件, 如果没有, 就用 `ssh-keygen -t rsa` 生成一对 (一路回车就好).

`cp id_rsa.pub authorized_keys`

## 5.2. 配置 itcast02 和 itcast03
首先也是先安装好 **ssh** 服务生成一对 `id_rsa`、`id_rsa.pub` 文件;

然后**不要**执行 `cp id_rsa.pub authorized_keys`, 而是执行 `ssh-copy-id -i ~/.ssh/id_rsa.pub martin@itcast01` 将公钥追加到 **itcast01** 的 **authorized_keys** 中.

操作好后到 itcast01 中 `vim authorized_keys` 可以看到里面已经多出了 itcast02 和 itcast03 的公钥了.

最后将 **authorized_keys** 远程拷贝到 itcast02 和 itcast03 中.

```
scp authorized_keys martin@itcast02:/home/martin/.ssh/authorized_keys
scp authorized_keys martin@itcast03:/home/martin/.ssh/authorized_keys
```
<br>
# 6. 启动集群
首次启动需要先在 NameNode 节点 (itcast01) 执行 NameNode 的格式化:

```
hdfs namenode -format       # 首次运行需要执行初始化，之后不需要
```
<br>
格式化成功后就可以通过以下命令启动集群了:

```
./start-dfs.sh
./start-yarn.sh
./mr-jobhistory-daemon.sh start historyserver
```
<br>
或者:

```
./start-all.sh
./mr-jobhistory-daemon.sh start historyserver
```
<br>
集群成功启动后可以在终端用 `JPS` 查看当前有哪些 Java 进程, NameNode 节点上应该有以下进程:

```
NameNode
SecondaryNameNode
ResourceManager
JobHistoryServer
```
<br>
而 DataNode 上应该有以下进程:

```
DataNode
NodeManager
```
<br>
停止集群命令如下:

```
stop-yarn.sh
stop-dfs.sh
mr-jobhistory-daemon.sh stop historyserver
```
<br>
或者:

```
stop-all.sh
mr-jobhistory-daemon.sh stop historyserver
```
<br>
还可以通过 `hdfs dfsadmin -report` 来查看所有 DataNode 的信息.

![](http://wiki.smallcpp.com/static/images/搭建Hadoop分布式实验环境/dfsadmin.png)

**Live datanodes** 表示当前集群有几个 DataNode 在运行.

# 7. 测试集群
集群启动成功后会提供 Web 界面来管理集群.

- [itcast01:50070](http://itcast01:50070) \-\- hdfs 管理界面
- [itcast01:8088](http://itcast01:8088) \-\- yarn 管理界面
- [itcast01:19888](http://itcast01:19888) \-\- jobhistory 管理界面


**hdfs 管理界面**

![](http://i61.tinypic.com/10fcr2s.jpg)

hadoop fs -ls /
hdfs://itcast01:9000

## 7.1. 上传文件
在 itcast01 (不一定是 itcast01, 可以集群中的任意一台进行测试) 的桌面上准备了一份大文件, 如 `ubuntu-16.04-desktop-amd64.iso`, 现在把它上传到 Hadoop 的 HDFS 文件系统上去.

`hadoop fs -put /home/martin/桌面/ubuntu-16.04-desktop-amd64.iso hdfs://itcast01:9000/ubuntu-amd64.iso`

上传文件到 `hdfs://itcast01:9000/` 并命名为 `ubuntu-amd64.iso`; 同样功能的命令除了 `put` 还有 `copyFromLocal` (过时).

`hdfs://itcast01:9000/` 表示的是 HDFS 的**根目录**, 可以简写成 `/`, 如上面的上传文件命令可以写成这样:

`hadoop fs -put /home/martin/桌面/ubuntu-16.04-desktop-amd64.iso /ubuntu-amd64.iso`

Hadoop 的 **HDFS** 系统使用起来就像是 Linux 的文件系统, 如 `hadoop fs -ls /` 查看的就是 HDFS 根目录下的列表, 切不要将它们混淆了, HDFS 的根目录可不在 Linux 的根目录 (`/`) 下, 是两套完全不同的体系.

上传完毕后可以在 Web 界面的文件管理模块可以看到变化:

![](http://wiki.smallcpp.com/static/images/搭建Hadoop分布式实验环境/hadoopfile.png)

![](http://wiki.smallcpp.com/static/images/搭建Hadoop分布式实验环境/hadoopinfo.png)

## 7.2. 下载文件
`hadoop fs -get /ubuntu-amd64.iso /home/martin/桌面/ubuntu-amd64.iso`

下载文件到 Linux 系统.

执行命令的时候, 可能会出现提示: `WARN hdfs.DFSClient: DFSInputStream has been closed already`.

不用管它, apache 也给出了说明:

![](http://i61.tinypic.com/344ql4k.jpg)

## 7.3. 测试 MR 作业
MR 使用 Java 进行开发, Hadoop 预置了一些测试 MR 作业 (就是一些 jar 包), 它们在: `/usr/itcast/hadoop-2.7.3/share/hadoop/mapreduce` 目录下.

创建一个文件 `vim words.txt`, 输入内容:

```
hello tom
hello jerry
hello kitty
hello world
hello martin
```
<br>
所有的 MR 都是执行在 **hdfs** 上的, 所以要先上传文件: `hadoop fs -put words.txt /words.txt`

`/usr/itcast/hadoop-2.7.3/share/hadoop/mapreduce` 目录下有个 `hadoop-mapreduce-examples-2.7.3.jar`, 里面有个 `wordcount` 方法, 可以用来统计单词个数.

```
cd /usr/itcast/hadoop-2.7.3/share/hadoop/mapreduce
hadoop jar hadoop-mapreduce-examples-2.7.3.jar wordcount /words.txt /result
```
<br>
第一个参数是待统计文件, 第二个参数是保存结果的目录.

执行完毕后, 查看下 hdfs 系统: `hadoop fs -ls /`

```
Found 4 items
drwxr-xr-x   - martin supergroup          0 2016-10-16 21:33 /result
drwxrwx---   - martin supergroup          0 2016-10-16 20:27 /tmp
-rw-r--r--   2 martin supergroup 1485881344 2016-10-16 21:01 /ubuntu-amd64.iso
-rw-r--r--   2 martin supergroup         59 2016-10-16 21:30 /words.txt

```
<br>
`/result` 就是刚生成的结果目录, 用 `hadoop fs -ls /result` 查看下里面的内容:

```
Found 2 items
-rw-r--r--   2 martin supergroup          0 2016-10-16 21:36 /result/_SUCCESS
-rw-r--r--   2 martin supergroup         47 2016-10-16 21:36 /result/part-r-00000
```
<br>
其中 `_SUCCESS` 表示 MR 作业执行成功, `part-r-00000` 为结果文件, 用 `hadoop fs -cat /result/part-r-00000` 查看下:

```
hello   5
jerry   1
kitty   1
martin  1
tom     1
world   1
```
<br>
当然也可以直接通过 Web 查看.

#  References
[Hadoop 安装教程_单机/伪分布式配置_Hadoop2.6.0/Ubuntu14.04](http://www.powerxing.com/install-hadoop/)<br>
[Hadoop 集群安装配置教程_Hadoop2.6.0/Ubuntu14.04](http://www.powerxing.com/install-hadoop-cluster/)
