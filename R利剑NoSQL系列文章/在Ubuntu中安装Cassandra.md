在Ubuntu中安装Cassandra
=========

[R利剑NoSQL系列文章](http://blog.fens.me/series-r-nosql/)，主要介绍通过R语言连接使用nosql数据库。涉及的NoSQL产品，包括[Redis](http://blog.fens.me/nosql-r-redis/), [MongoDB](http://blog.fens.me/nosql-r-mongodb/), [HBase](http://blog.fens.me/nosql-r-hbase), [Hive](http://blog.fens.me/nosql-r-hive/), [Cassandra](http://blog.fens.me/nosql-r-cassandra/), [Neo4j](http://blog.fens.me/nosql-r-neo4j/)。希望通过我的介绍让广大的R语言爱好者，有更多的开发选择，做出更多地激动人心的应用。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/linux-cassandra-install/

![在Ubuntu中安装Cassandra](http://blog.fens.me/wp-content/uploads/2014/03/linux-cassandra.png)

#### 前言

Cassandra是由Facebook开发，然后开源的一套分步式数据存储系统，用于海量数据的高伸展性存储。Cassandra的无中心设计，一致性哈希，BloomFilter等技术是亮点。

#### 目录

1. 在Ubuntu中环境准备
2. 下载cassandra软件包
3. 配置Cassandra
4. 设置环境变量
5. 启动cassandra服务器
6. 用客户端访问cassandra

## 1. 在Ubuntu中环境准备

Cassandra是基于Java开发的NoSQL数据库软件，Cassandra没有提供Windows系统安装版本。我在这里也只介绍Cassandra在Linux Ubuntu系统中的安装。

由于Cassandra是用Java开发的，因此我们需要先安装好Java的环境，Java的安装请参考文章：[在Ubuntu中安装Java(JDK)](http://blog.fens.me/linux-java-install/)

Cassandra没有提供apt的软件源安装，我们需要自己去官方网络下载Cassandra软件包进行安装。Cassandra下载页：http://cassandra.apache.org/download/

下载Cassandra时，发现cassandra有两个版本在并行发布。因此我们有2种选择，最新2.0分支的版本2.0.6(2014-03-10发布)；或者1.2分支的版本1.2.15(2014-02-07)。

由于我不清楚2.0分支，就底有哪些改进；所以本文将以1.2分支1.2.15版本为例，进行单机的安装和配置。

系统环境：

* Linux Ubuntu 12.04.2 LTS 64bit server
* Java JDK 1.6.0_45

## 2. 下载cassandra软件包

下载cassandra

```{bash}

# 1.2.15版本下载
~ wget http://apache.dataguru.cn/cassandra/1.2.15/apache-cassandra-1.2.15-bin.tar.gz

# 解压软件包
~ tar xvf apache-cassandra-1.2.15-bin.tar.gz

# 查看文件和目录
~ tree -L 2
.
├── apache-cassandra-1.2.15
│   ├── bin
│   ├── CHANGES.txt
│   ├── conf
│   ├── interface
│   ├── javadoc
│   ├── lib
│   ├── LICENSE.txt
│   ├── NEWS.txt
│   ├── NOTICE.txt
│   ├── pylib
│   ├── README.txt
│   └── tools
├── apache-cassandra-1.2.15-bin.tar.gz

# 对Cassandra解压目录改名
~ mv apache-cassandra-1.2.15/ cassandra1215

# 进行目录
~ cd cassandra1215/
```

## 3. 配置Cassandra

设置cassandra数据目录

* data_file_directories：为数据文件目录
* commitlog_directory：为日志文件目录
* saved_caches_directory：为缓存文件目录

用vi打开cassandra的配置文件cassandra.yaml

```{bash}

~ vi conf/cassandra.yaml

data_file_directories:
    - /var/lib/cassandra/data
commitlog_directory: /var/lib/cassandra/commitlog
saved_caches_directory: /var/lib/cassandra/saved_caches
```

确认操作系统中，这几个目录已被创建。
同时确认/var/log/cassandra/目录，对于操作用户是可写的。

```{bash}

# 新建目录
~ sudo mkdir -p /var/lib/cassandra/data
~ sudo mkdir -p /var/lib/cassandra/saved_caches
~ sudo mkdir -p /var/lib/cassandra/commitlog
~ sudo mkdir -p /var/log/cassandra/

# 把目录归属改成操作用户
~ sudo chown -R conan:conan /var/lib/cassandra
~ sudo chown -R conan:conan /var/log/cassandra/

# 相看目录权限
~ ll /var/lib/cassandra
drwxr-xr-x  2 conan conan 4096  3月 22 06:23 commitlog/
drwxr-xr-x  2 conan conan 4096  3月 22 06:23 data/
drwxr-xr-x  2 conan conan 4096  3月 22 06:23 saved_caches/
```

## 4. 设置环境变量

```{bash}

~ sudo vi /etc/environment
CASSANDRA_HOME=/home/conan/tookit/cassandra1215

# 让环境变量生效
~ . /etc/environment

#查看环境变量
~ echo $CASSANDRA_HOME
/home/conan/tookit/cassandra1215
```

## 5. 启动cassandra服务器

通过命令，启动cassandra服务器

```{bash}

#注：-f参数是绑定到console，不加-f则是后台启动。
~ bin/cassandra

 INFO 06:28:08,777 Logging initialized
 INFO 06:28:08,795 JVM vendor/version: Java HotSpot(TM) 64-Bit Server VM/1.6.0_45
 INFO 06:28:08,799 Heap size: 2051014656/2051014656
 INFO 06:28:08,799 Classpath: bin/../conf:bin/../build/classes/main:bin/../build/classes/thrift:bin/../lib/antlr-3.2.jar:bin/../lib/apache-cassandra-1.2.15.jar:bin/../lib/apache-cassandra-clientutil-1.2.15.jar:bin/../lib/apache-cassandra-thrift-1.2.15.jar:bin/../lib/avro-1.4.0-fixes.jar:bin/../lib/avro-1.4.0-sources-fixes.jar:bin/../lib/commons-cli-1.1.jar:bin/../lib/commons-codec-1.2.jar:bin/../lib/commons-lang-2.6.jar:bin/../lib/compress-lzf-0.8.4.jar:bin/../lib/concurrentlinkedhashmap-lru-1.3.jar:bin/../lib/guava-13.0.1.jar:bin/../lib/high-scale-lib-1.1.2.jar:bin/../lib/jackson-core-asl-1.9.2.jar:bin/../lib/jackson-mapper-asl-1.9.2.jar:bin/../lib/jamm-0.2.5.jar:bin/../lib/jbcrypt-0.3m.jar:bin/../lib/jline-1.0.jar:bin/../lib/json-simple-1.1.jar:bin/../lib/libthrift-0.7.0.jar:bin/../lib/log4j-1.2.16.jar:bin/../lib/lz4-1.1.0.jar:bin/../lib/metrics-core-2.2.0.jar:bin/../lib/netty-3.6.6.Final.jar:bin/../lib/servlet-api-2.5-20081211.jar:bin/../lib/slf4j-api-1.7.2.jar:bin/../lib/slf4j-log4j12-1.7.2.jar:bin/../lib/snakeyaml-1.6.jar:bin/../lib/snappy-java-1.0.5.jar:bin/../lib/snaptree-0.1.jar:bin/../lib/jamm-0.2.5.jar
 INFO 06:28:08,801 JNA not found. Native methods will be disabled.
 INFO 06:28:08,813 Loading settings from file:/home/conan/tookit/cassandra1215/conf/cassandra.yaml
 
// 省略日志
```

查看cassandra系统进程

```{bash}

# 查看cassandra系统进程
~ ps -axu|grep cassandra

conan     5983 18.1  2.1 4499456 172832 pts/1  Sl   06:31   0:05 java -ea -javaagent:bin/../lib/jamm-0.2.5.jar -XX:+UseThreadPriorities -XX:ThreadPriorityPolicy=42 -Xms1996M -Xmx1996M -Xmn400M -XX:+HeapDumpOnOutOfMemoryError -Xss256k -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:+CMSParallelRemarkEnabled -XX:SurvivorRatio=8 -XX:MaxTenuringThreshold=1 -XX:CMSInitiatingOccupancyFraction=75 -XX:+UseCMSInitiatingOccupancyOnly -XX:+UseTLAB -Djava.net.preferIPv4Stack=true -Dcom.sun.management.jmxremote.port=7199 -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false -Dlog4j.configuration=log4j-server.properties -Dlog4j.defaultInitOverride=true -cp bin/../conf:bin/../build/classes/main:bin/../build/classes/thrift:bin/../lib/antlr-3.2.jar:bin/../lib/apache-cassandra-1.2.15.jar:bin/../lib/apache-cassandra-clientutil-1.2.15.jar:bin/../lib/apache-cassandra-thrift-1.2.15.jar:bin/../lib/avro-1.4.0-fixes.jar:bin/../lib/avro-1.4.0-sources-fixes.jar:bin/../lib/commons-cli-1.1.jar:bin/../lib/commons-codec-1.2.jar:bin/../lib/commons-lang-2.6.jar:bin/../lib/compress-lzf-0.8.4.jar:bin/../lib/concurrentlinkedhashmap-lru-1.3.jar:bin/../lib/guava-13.0.1.jar:bin/../lib/high-scale-lib-1.1.2.jar:bin/../lib/jackson-core-asl-1.9.2.jar:bin/../lib/jackson-mapper-asl-1.9.2.jar:bin/../lib/jamm-0.2.5.jar:bin/../lib/jbcrypt-0.3m.jar:bin/../lib/jline-1.0.jar:bin/../lib/json-simple-1.1.jar:bin/../lib/libthrift-0.7.0.jar:bin/../lib/log4j-1.2.16.jar:bin/../lib/lz4-1.1.0.jar:bin/../lib/metrics-core-2.2.0.jar:bin/../lib/netty-3.6.6.Final.jar:bin/../lib/servlet-api-2.5-20081211.jar:bin/../lib/slf4j-api-1.7.2.jar:bin/../lib/slf4j-log4j12-1.7.2.jar:bin/../lib/snakeyaml-1.6.jar:bin/../lib/snappy-java-1.0.5.jar:bin/../lib/snaptree-0.1.jar org.apache.cassandra.service.CassandraDaemon

# 查看系统端口
~ netstat -nlt|grep 9160
tcp        0      0 127.0.0.1:9160          0.0.0.0:*               LISTEN
```

## 6. 用客户端访问cassandra

通过客户端程序访问Cassandra服务器

```{bash}

~ bin/cassandra-cli
Connected to: "Test Cluster" on 127.0.0.1/9160
Welcome to Cassandra CLI version 1.2.15

Type 'help;' or '?' for help.
Type 'quit;' or 'exit;' to quit.

# 查看命令行帮助
[default@unknown] ?

Getting around:
?                       Display this help.
help;                   Display this help.
help ;         Display command-specific help.
exit;                   Exit this utility.
quit;                   Exit this utility.

Commands:
assume                  Apply client side validation.
connect                 Connect to a Cassandra node.
consistencylevel        Sets consisteny level for the client to use.
count                   Count columns or super columns.
create column family    Add a column family to an existing keyspace.
create keyspace         Add a keyspace to the cluster.
del                     Delete a column, super column or row.
decr                    Decrements a counter column.
describe cluster        Describe the cluster configuration.
describe                Describe a keyspace and its column families or column family in current keyspace.
drop column family      Remove a column family and its data.
drop keyspace           Remove a keyspace and its data.
drop index              Remove an existing index from specific column.
get                     Get rows and columns.
incr                    Increments a counter column.
list                    List rows in a column family.
set                     Set columns.
show api version        Show the server API version.
show cluster name       Show the cluster name.
show keyspaces          Show all keyspaces and their column families.
show schema             Show a cli script to create keyspaces and column families.
truncate                Drop the data in a column family.
update column family    Update the settings for a column family.
update keyspace         Update the settings for a keyspace.
use                     Switch to a keyspace.
```

单节的Cassandra，我们已经成功能安装在Linux Ubuntu系统中。

#### 转载请注明出处：http://blog.fens.me/linux-cassandra-install/





