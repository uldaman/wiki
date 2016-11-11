---
title: Maven
layout: page
date: 2016-11-09 21:28
---

[TOC]

# 介绍
maven 是一个基于 POM (项目对象模型) 的项目管理和构建自动化工具, 通过它便捷的管理项目生命周期. 即项目的 jar 包依赖、开发、测试、打包和发布.

可以下载最新版本, 也可以下载经典版本: [maven 3.0.5](http://archive.apache.org/dist/maven/maven-3/3.0.5/binaries/)

对于我个人来说, 使用了 maven 最大的好处就是不再需要上网单独下载 jar 包, 只需要在配置文件 `pom.xml` 中配置 jar 包的依赖关系, 就可以自动的下载 jar 包到我们的项目中. 当移植项目时也不再需要来回的拷贝依赖包, 只需要复制这个 pom.xml 就可以自动的下载这些 jar 包. 这样做还能避免依赖包版本的不一致的问题.

maven 通过在 `pom.xml` 中配置特定的标识来定义项目名称, 这样既可以唯一的匹配其他的 jar 包, 也可以通过发布, 使别人能使用自己发布的 jar 包:

```xml
<groupId>com.smallcpp</groupId>
<artifactId>maventest</artifactId>
<version>0.0.1</version>
<packaging>jar</packaging>
<name>maventest</name>
<url>http://maven.apache.org</url>
```
<br>
主要就是上面的几个参数:

- **groupId**: 所述的项目名称, 由于有的项目并不是一个 jar 包构成的, 而是由很多的 jar 包组成的. 因此这个 groupId 就是整个项目的名称.
- **artifactId**: 包的名称.
- **version**: 版本号.
- **packaging**: 包的类型, 一般都是jar, 也可以是war之类的. 如果不填, 默认就是jar.
- **name** 和 **url**: 一个是名称, 一个是 maven 的地址.

# 配置
下载 maven 后解压到任意位置 (例如 D:/apache-maven-3.0.5), 然后在修改环境变量:

```
M2_HOME: D/apache-maven-3.0.
PATH: %M2_NOME%/bin
```
<br>
然后打开 cmd 查看是否设置成功:

```
mvn -version 查看版本
```
<br>
上面的介绍中已经提到过了, maven 会自动下载 jar 包, 所以要配置下本地 jar 包的存放目录, maven 的设置文件是 `M2_HOME/conf/settings.xml`, 我们找到其中的 `localRepository` 标签, 改成:

```
<localRepository>F:/.m2/repositorys</localRepository>
```
<br>
maven 中三个仓库的概念: **本地仓库** \-\> **远程仓库** \-\> **中央仓库**, maven 查找 jar 包也是按这个顺序.

本地仓库就是我们上面配置的 `localRepository`, 当 maven 在这个仓库没有找到想要的 jar 包时, 就会去远程仓库寻找, 远程仓库的地址需要我们自己设置, 如果没有设置 (或者在远程仓库也没找到想要的 jar 包), 就会再去中央仓库中找, 如果还没找到, 就会报错.

**远程仓库** 可以在项目的 `pom.xml` 中设置, 但由于远程仓库的设置通常是想达到全局的效果, 所以也可以在 `M2_HOME/conf/settings.xml` 中通过 **profile** 设置 (具体的 profile 详细说明参见: [Maven 简介 —— profile 介绍](http://elim.iteye.com/blog/1900568)).

如下, 我们配置了阿里云做为远程仓库, 将其 **id** 设为 **alimaven**, 并且在 `activeProfile` 标签中激活这个 **id** (注意, 远程仓库需要 "激活" 才能使用, 除了 `activeProfile` 标签, 其它激活方式参见上面的 *Maven 简介 —— profile 介绍*).

```xml
<profiles>
    <profile>
        <id>alimaven</id>
        <repositories>
            <repository>
                <id>nexus</id>
                <name>local private nexus</name>
                <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
                <releases>
                    <enabled>true</enabled>
                </releases>
                <snapshots>
                    <enabled>false</enabled>
                </snapshots>
            </repository>
        </repositories>
        <pluginRepositories>
            <pluginRepository>
                <id>nexus</id>
                <name>local private nexus</name>
                <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
                <releases>
                    <enabled>true</enabled>
                </releases>
                <snapshots>
                    <enabled>false</enabled>
                </snapshots>
            </pluginRepository>
        </pluginRepositories>
    </profile>
</profiles>

<activeProfiles>
<activeProfile>alimaven</activeProfile>
</activeProfiles>
```
<br>
另外, 中央仓库其实就是 maven 默认给我们配好的 **id** 为 **central** 的仓库, 它的配置大概像下面这样 (这个配置在 maven 2.0 版本中位于 `${M2_HOME}/lib/maven-2.0.10-uber.jar/pom-4.0.0.xml`, 后面的版本我没找到...不过应该也差不多):

```xml
<repositories>
  <repository>
    <id>central</id>
    <name>Maven Repository Switchboard</name>
    <layout>default</layout>
    <url>http://repo1.maven.org/maven2</url>
    <snapshots>
      <enabled>false</enabled>
    </snapshots>
  </repository>
</repositories>
```
<br>
可以看到中央仓库的 ID 为 `central`.

maven 还提供镜像的配置, 让我们可以为仓库设置**镜像**, 在 `M2_HOME/conf/settings.xml` 中的 `mirror` 标签配置 (老实说, 为远程仓库配置镜像并没什么卵用, 反正地址都是我自己配, 给中央仓库设计镜像也没有必要, 还不如将镜像地址当做一个远程仓库设置, 这样镜像中找不到 jar 时还会去中央仓库再找一次, 多好 . . .).

```xml
<mirrors>
    <mirror>
          <id>alimaven</id>
          <name>aliyun maven</name>
          <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
          <mirrorOf>central</mirrorOf>
    </mirror>
</mirrors>
```
<br>
`mirrorOf` 标签指定要镜像哪个仓库.

# 生命周期
最开始说到 maven 可以便捷的管理项目生命周期, maven 有三套相互独立的生命周期, 请注意这里说的是 "三套", 而且 "相互独立", 初学者容易将 maven 的生命周期看成一个整体, 其实不然;<br>
这三套生命周期分别是：

- **Clean Lifecycle** 在进行真正的构建之前进行一些清理工作
- **Default Lifecycle** (或称 Build Lifecycle) 构建的核心部分, 编译, 测试, 打包, 部署等等
- **Site Lifecycle** 生成项目报告, 站点, 发布站点

**Default Lifecycle** 定义了真正构建时所需要执行的大部分步骤, 它是生命周期中最核心的部分, 它大概如下图这样:

![](http://wiki.smallcpp.com/static/images/MAVEN/Default阶段.png)

在每段生命周期内 maven 都提供了 mvn **命令**来维护该阶段, 对于 Default Lifecycle:

- **generate-sources**: 通常是通过插件支持创建额外的源代码
- **compile**: 编译项目应用代码
- **test-compile**: 编译项目单元测试代码
- **test**: 运行项目单元测试 (一般为 Junit 测试)
- **package**: 打包项目可以执行代码 (以 Jar/War/Ear 形式)
- **integration-test**: 如有需要处理及部署应用以便执行系统集成测试
- **install**: 将应用打包发布到本地 maven 软件包以便其它 maven 引用 (注意 Install 不是指安装 Java 应用)
- **deploy**: 发布到远程 maven 软件包以便其他 maven 项目下载引用

阶段内的命令是**有序**的, 每个命令的调用会自动调用其前面的阶段命令, 所以大部分情况下, 我们使用 `mvn install` 就可以了.

另一个常用的命令是 `mvn clean` 用来清理项目环境, 如删除项目目录下的 target 目录 (已发布到本地仓库的需要自行控制), 但它属于 Clean Lifecycle 阶段的命令, 和 `mvn install` 在不同的生命周期, 所以执行 `mvn install` 时不会主动去执行 `mvn clean`, 因此, 我们通常和 install 组合使用:

```
mvn clean install
```
<br>
# Eclipse 插件
Eclipse 提供了 maven 插件来方便我们使用, 该插件可以让我们在 Eclipse 上快速新建 maven 项目、导入 maven 项目及转换 maven 项目等.

新版本的 Eclipse 已集成了 maven 插件, 无须再手动下载, 我们只需配置一下, 主要设置三样: 是否自动下载源码、本地 maven 环境 (Eclipse 有内置的, 不过建议用我们下载的 3.0.5) 以及配置文件目录.

![](http://wiki.smallcpp.com/static/images/MAVEN/MAVEN插件设置1.png)

![](http://wiki.smallcpp.com/static/images/MAVEN/MAVEN插件设置2.png)

![](http://wiki.smallcpp.com/static/images/MAVEN/MAVEN插件设置3.png)

我们可以新建 maven 项目, 也可以从现有的 java 项目转换:

从现有项目**转换**:

![](http://wiki.smallcpp.com/static/images/MAVEN/现有项目转换MAVEN.png)

或者**新建** maven 项目:

![](http://wiki.smallcpp.com/static/images/MAVEN/新建MAVEN.png)

![](http://wiki.smallcpp.com/static/images/MAVEN/简单项目.png)

![](http://wiki.smallcpp.com/static/images/MAVEN/创建POM.png)

maven 项目的左上角有个 **m** 标记, 并且项目下有 `pom.xml` 文件:

![](http://wiki.smallcpp.com/static/images/MAVEN/MAVEN项目结构.png)

并且项目右键多了个一些 maven 选项:

![](http://wiki.smallcpp.com/static/images/MAVEN/常用MAVEN菜单.png)

![](http://wiki.smallcpp.com/static/images/MAVEN/MAVENRUNAS.png)

说下 `mvn build` 这个选项, mvn 是没有这个命令的, 这个命令菜单是 Eclipse 提供一个接口, 选择该菜单就能通过配置来执行任意 mvn 命令, 如 `mvn clean install`:

![](http://wiki.smallcpp.com/static/images/MAVEN/RUNCONFIG.png)

如果配置错了, 可以在下面这里进行修改:

![](http://wiki.smallcpp.com/static/images/MAVEN/RUNCONFIG0.png)

![](http://wiki.smallcpp.com/static/images/MAVEN/RUNCONFIG1.png)

使用了 Eclipse 插件后, 构建项目时会根据 `pom.xml` 的配置自动下载依赖包, 但由于我们关闭了 Eclipse 的**自动构建**, 所以需要手动触发下 maven 去下载 jar 依赖, 执行 Default Lifecycle 阶段的任意命令都能触发下载, 也可以通过下面图示的菜单去触发下载:

![](http://wiki.smallcpp.com/static/images/MAVEN/JAR下载.png)

然后就能在 **progress** 视图看到 Eclipse 进行了下载操作:

![](http://wiki.smallcpp.com/static/images/MAVEN/DOWNLOADJAR.png)

或者

![](http://wiki.smallcpp.com/static/images/MAVEN/DOWNLOADJAR1.png)

# 添加依赖
在 Eclipse 中双击打开 `pom.xml` 可以以可视化的方式添加依赖 (也可以在项目右键的 maven 菜单中使用该方式):

![](http://wiki.smallcpp.com/static/images/MAVEN/添加依赖0.png)

打开添加依赖窗口后, 在下图中红框处输入内容可以检索本地仓库, 如果本地仓库没有则检索不到:

![](http://wiki.smallcpp.com/static/images/MAVEN/添加依赖1.png)

检索不到时候就需要手动填写所依赖 jar 包的 maven 特征:

![](http://wiki.smallcpp.com/static/images/MAVEN/添加依赖2.png)

jar 包的特征可以在 [mvn repository](https://mvnrepository.com/) 搜索得到.

当然也可以在 `pom.xml` 中手动添加:

```xml
<dependencies>
  <dependency>
      <groupId>org.apache.hadoop</groupId>
      <artifactId>hadoop-common</artifactId>
      <version>2.7.3</version>
  </dependency>
</dependencies>
```
<br>
