Hadoop历史版本安装
===========

[Hadoop家族系列文章](http://blog.fens.me/series-hadoop-family/)，主要介绍Hadoop家族产品，常用的项目包括Hadoop, Hive, Pig, HBase, Sqoop, Mahout, Zookeeper, Avro, Ambari, Chukwa，新增加的项目包括，YARN, Hcatalog, Oozie, Cassandra, Hama, Whirr, Flume, Bigtop, Crunch, Hue等。

从2011年开始，中国进入大数据风起云涌的时代，以Hadoop为代表的家族软件，占据了大数据处理的广阔地盘。开源界及厂商，所有数据软件，无一不向Hadoop靠拢。Hadoop也从小众的高富帅领域，变成了大数据开发的标准。在Hadoop原有技术基础之上，出现了Hadoop家族产品，通过“大数据”概念不断创新，推出科技进步。

作为IT界的开发人员，我们也要跟上节奏，抓住机遇，跟着Hadoop一起雄起！

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/hadoop-history-source-install/

![Hadoop历史版本安装](http://blog.fens.me/wp-content/uploads/2013/10/hadoop-history.png)

#### 前言

介绍Hadoop安装的文章，已经写过2篇了，老生常谈的话题又被拿出来了。这次要重新安装Hadoop-1.1.2的历史版本，来满足Mahout-0.8版本的依赖要求。本来只想简单说几句，不过遇到了几个小问题，因此写篇文章总结一下吧。

Hadoop安装的其他文章：

* [RHadoop实践系列之一 Hadoop环境搭建](http://blog.fens.me/rhadoop-hadoop/)
* [创建Hadoop母体虚拟机](http://blog.fens.me/hadoop-base-kvm/)

#### 目录

1. 找到Hadoop历史版本
2. 用源代码构建Hadoop环境
3. 快速Hadoop配置环境脚本
4. 为win环境编译hadoop-core.jar

## 1. 找到Hadoop历史版本

这里我需要的Hadoop版本是1.1.2。打开hadoop的下载页面

```{bash}
http://www.apache.org/dyn/closer.cgi/hadoop/common/
```

随便打开一个下载镜像，我们都找不到1.1.2这个版本。

![](http://blog.fens.me/wp-content/uploads/2013/10/hadoop-download.png)

看看github上的情况

![](http://blog.fens.me/wp-content/uploads/2013/10/hadoop-github.png)

找到release-1.1.2

```{bash}
https://github.com/apache/hadoop-common/releases/tag/release-1.1.2
```

下载源代码：

```{bash}
~ wget https://github.com/apache/hadoop-common/archive/release-1.1.2.tar.gz
```

查看branch-1.1

```{bash}
https://github.com/apache/hadoop-common/tree/branch-1.1
```

## 2. 用源代码构建Hadoop环境

注：github上面最新hadoop-3.0.0-SNAPSHOT，包结构已经完全改变，用Maven代替了Ant+Ivy的构建过程。

### Linux系统环境：

* Linux Ubuntu 64bit Server 12.04.2 LTS
* Java 1.6.0_29
* Ant 1.8.4

### 安装Hadoop

```{bash}

~ tar xvf release-1.1.2.tar.gz
~ mkdir /home/conan/hadoop/
~ mv hadoop-common-release-1.1.2 /home/conan/hadoop/
~ cd /home/conan/hadoop/
~ mv hadoop-common-release-1.1.2/ hadoop-1.1.2
```

### 查看hadoop目录

```{bash}
~ ls -l
total 648
drwxrwxr-x  2 conan conan   4096 Mar  6  2013 bin
-rw-rw-r--  1 conan conan 120025 Mar  6  2013 build.xml
-rw-rw-r--  1 conan conan 467130 Mar  6  2013 CHANGES.txt
drwxrwxr-x  2 conan conan   4096 Oct  3 02:31 conf
drwxrwxr-x  2 conan conan   4096 Oct  3 02:28 ivy
-rw-rw-r--  1 conan conan  10525 Mar  6  2013 ivy.xml
drwxrwxr-x  4 conan conan   4096 Mar  6  2013 lib
-rw-rw-r--  1 conan conan  13366 Mar  6  2013 LICENSE.txt
drwxrwxr-x  2 conan conan   4096 Oct  3 03:35 logs
-rw-rw-r--  1 conan conan    101 Mar  6  2013 NOTICE.txt
-rw-rw-r--  1 conan conan   1366 Mar  6  2013 README.txt
-rw-rw-r--  1 conan conan   7815 Mar  6  2013 sample-conf.tgz
drwxrwxr-x 16 conan conan   4096 Mar  6  2013 src
```

在根目录下面，没有hadoop-*.jar的各种类库，也没有依赖库。

### 执行Ant进行编译

```{bash}
~ sudo apt-get install libtool
~ ant
```

要等几分钟，然后build成功。

### 查看生成的build目录

```{bash}
~ ls -l build
drwxrwxr-x  3 conan conan    4096 Oct  3 04:06 ant
drwxrwxr-x  2 conan conan    4096 Oct  3 04:02 c++
drwxrwxr-x  3 conan conan    4096 Oct  3 04:05 classes
drwxrwxr-x 13 conan conan    4096 Oct  3 04:06 contrib
drwxrwxr-x  2 conan conan    4096 Oct  3 04:05 empty
drwxrwxr-x  2 conan conan    4096 Oct  3 04:02 examples
-rw-rw-r--  1 conan conan     423 Oct  3 04:05 hadoop-client-1.1.3-SNAPSHOT.jar
-rw-rw-r--  1 conan conan 4035744 Oct  3 04:05 hadoop-core-1.1.3-SNAPSHOT.jar
-rw-rw-r--  1 conan conan     426 Oct  3 04:05 hadoop-minicluster-1.1.3-SNAPSHOT.jar
-rw-rw-r--  1 conan conan  306827 Oct  3 04:05 hadoop-tools-1.1.3-SNAPSHOT.jar
drwxrwxr-x  4 conan conan    4096 Oct  3 04:02 ivy
drwxrwxr-x  3 conan conan    4096 Oct  3 04:02 src
drwxrwxr-x  6 conan conan    4096 Oct  3 04:02 test
drwxrwxr-x  3 conan conan    4096 Oct  3 04:05 tools
drwxrwxr-x  9 conan conan    4096 Oct  3 04:02 webapps
```

发现hadoop-*.jar都是hadoop-*-1.1.3-SNAPSHOT.jar结尾的，合理的解释就是：上一个版本的release就是下一个版本的SNAPSHOT。

我们修改一下build.xml文件31行，把包名改成hadoop-*-1.1.2.jar，再重新ant

```{bash}
~ vi build.xml

~ rm -rf build
~ ant
```

### 把生成的hadoop-*-1.1.2.jar复制到根目录

```{bash}

~ cp build/*.jar .
~ ls -l
drwxrwxr-x  2 conan conan    4096 Oct  3 04:24 bin
drwxrwxr-x 13 conan conan    4096 Oct  3 04:21 build
-rw-rw-r--  1 conan conan  120016 Oct  3 04:20 build.xml
-rw-rw-r--  1 conan conan  467130 Mar  6  2013 CHANGES.txt
drwxrwxr-x  2 conan conan    4096 Oct  3 02:31 conf
-rw-rw-r--  1 conan conan     414 Oct  3 04:24 hadoop-client-1.1.2.jar
-rw-rw-r--  1 conan conan 4035726 Oct  3 04:24 hadoop-core-1.1.2.jar
-rw-rw-r--  1 conan conan     417 Oct  3 04:24 hadoop-minicluster-1.1.2.jar
-rw-rw-r--  1 conan conan  306827 Oct  3 04:24 hadoop-tools-1.1.2.jar
drwxrwxr-x  2 conan conan    4096 Oct  3 02:28 ivy
-rw-rw-r--  1 conan conan   10525 Mar  6  2013 ivy.xml
drwxrwxr-x  4 conan conan    4096 Mar  6  2013 lib
-rw-rw-r--  1 conan conan   13366 Mar  6  2013 LICENSE.txt
drwxrwxr-x  3 conan conan    4096 Oct  3 04:28 logs
-rw-rw-r--  1 conan conan     101 Mar  6  2013 NOTICE.txt
-rw-rw-r--  1 conan conan    1366 Mar  6  2013 README.txt
-rw-rw-r--  1 conan conan    7815 Mar  6  2013 sample-conf.tgz
drwxrwxr-x 16 conan conan    4096 Mar  6  2013 src
```

这个就生成好了，hadoop环境，和我们之前下载的就一样了。

## 3. 快速Hadoop配置环境脚本

* 1. 配置环境变量
* 2. Hadoop的3个配置文件
* 3. 创建Hadoop目录
* 4. 配置hostname和hosts
* 5. 生成SSH免登陆
* 6. 第一次启动格式化HDFS
* 7. 启动Hadoop的服务

### 下面的脚本要写在一起

```{bash}
~ sudo vi /etc/environment
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/home/conan/toolkit/jdk16/bin:/home/conan/toolkit/ant184/bin:/home/conan/toolkit/maven3/bin:/home/conan/toolkit/tomcat7/bin:/home/conan/hadoop/hadoop-1.1.2/bin"
JAVA_HOME=/home/conan/toolkit/jdk16
ANT_HOME=/home/conan/toolkit/ant184
MAVEN_HOME=/home/conan/toolkit/maven3
NUTCH_HOME=/home/conan/toolkit/nutch16
TOMCAT_HOME=/home/conan/toolkit/tomcat7
HADOOP_HOME=/home/conan/hadoop/hadoop-1.1.2
HADOOP_CMD=/home/conan/hadoop/hadoop-1.1.2/bin/hadoop
HADOOP_STREAMING=/home/conan/hadoop/hadoop-1.1.2/contrib/streaming/hadoop-streaming-1.1.2.jar

~ . /etc/environment

~ vi conf/core-site.xml
<configuration>
<property>
<name>fs.default.name</name>
<value>hdfs://master:9000</value>
</property>
<property>
<name>hadoop.tmp.dir</name>
<value>/home/conan/hadoop/tmp</value>
</property>
<property>
<name>io.sort.mb</name>
<value>256</value>
</property>
</configuration>

~ vi conf/hdfs-site.xml
<configuration>
<property>
<name>dfs.data.dir</name>
<value>/home/conan/hadoop/data</value>
</property>
<property>
<name>dfs.replication</name>
<value>1</value>
</property>
<property>
<name>dfs.permissions</name>
<value>false</value>
</property>
</configuration>

~ vi conf/mapred-site.xml
<configuration>
<property>
<name>mapred.job.tracker</name>
<value>hdfs://master:9001</value>
</property>
</configuration>

~ mkdir /home/conan/hadoop/data
~ mkdir /home/conan/hadoop/tmp
~ sudo chmod 755 /home/conan/hadoop/data/
~ sudo chmod 755 /home/conan/hadoop/tmp/

~ sudo hostname master
~ sudo vi /etc/hosts
192.168.1.210   master
127.0.0.1       localhost

~ ssh-keygen -t rsa
~ cat ~/.ssh/id_rsa.pub >> authorized_keys

~ bin/hadoop namenode -format
~ bin/start-all.sh
```

### 检查hadoop运行状态

```{bash}
~ jps
15574 DataNode
16324 Jps
15858 SecondaryNameNode
16241 TaskTracker
15283 NameNode
15942 JobTracker

~ bin/hadoop dfsadmin -report
Configured Capacity: 18751434752 (17.46 GB)
Present Capacity: 14577520655 (13.58 GB)
DFS Remaining: 14577491968 (13.58 GB)
DFS Used: 28687 (28.01 KB)
DFS Used%: 0%
Under replicated blocks: 0
Blocks with corrupt replicas: 0
Missing blocks: 0

-------------------------------------------------
Datanodes available: 1 (1 total, 0 dead)

Name: 192.168.1.210:50010
Decommission Status : Normal
Configured Capacity: 18751434752 (17.46 GB)
DFS Used: 28687 (28.01 KB)
Non DFS Used: 4173914097 (3.89 GB)
DFS Remaining: 14577491968(13.58 GB)
DFS Used%: 0%
DFS Remaining%: 77.74%
Last contact: Thu Oct 03 05:03:50 CST 2013
```

## 4. 为win环境编译hadoop-core.jar

在win环境开发中，经常有一个

我们需要修改FileUtil.java文件，注释688行–693行

![](http://blog.fens.me/wp-content/uploads/2013/10/hadoop-win-code.png)

```{bash}

~ vi src/core/org/apache/hadoop/fs/FileUtil.java

685 private static void checkReturnValue(boolean rv, File p,
686                                        FsPermission permission
687                                        ) throws IOException {
688   /*  if (!rv) {
689       throw new IOException("Failed to set permissions of path: " + p +
690                             " to " +
691                             String.format("%04o", permission.toShort()));
692     }
693   */
694   }
```

用ant重新打包后，就生成了可以在win运行的hadoop-core-1.1.2.jar！使用方法可以参考文章：[用Maven构建Hadoop项目](http://blog.fens.me/hadoop-maven-eclipse/)，我们后面一直会用到！

#### 转载请注明出处：http://blog.fens.me/hadoop-history-source-install/
