Cassandra单集群实验2个节点
==============

[R利剑NoSQL系列文章](http://blog.fens.me/series-r-nosql/)，主要介绍通过R语言连接使用nosql数据库。涉及的NoSQL产品，包括[Redis](http://blog.fens.me/nosql-r-redis/), [MongoDB](http://blog.fens.me/nosql-r-mongodb/), [HBase](http://blog.fens.me/nosql-r-hbase), [Hive](http://blog.fens.me/nosql-r-hive/), [Cassandra](http://blog.fens.me/nosql-r-cassandra/), [Neo4j](http://blog.fens.me/nosql-r-neo4j/)。希望通过我的介绍让广大的R语言爱好者，有更多的开发选择，做出更多地激动人心的应用。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/cassandra-clustor/

![Cassandra单集群实验2个节点](http://blog.fens.me/wp-content/uploads/2013/07/cassandra-title.png)

#### 前言

Apache Cassandra是一套开源分布式Key-Value存储系统。它最初由Facebook开发，用于储存特别大的数据。主要特性：分布式，基于column的结构化，高伸展性。作为NoSQL的一支代表，虽然现在已经被hbase超越，但Cassandra的很多的设计思想是非常值得我们学习和借鉴的。感谢tigerfish老师的详细讲解，让我收获颇多！

Cassandra中非常有用的几个概念：一致性哈希，Gossip协议，Snitch，复制策略，DHT，BloomFilter。

#### 目录

1. Cassandra集群实验2个节点
2. 实验过程的错误及修复

## 1. Cassandra集群实验2个节点

### 1. 下载Cassandra并配置JAVA环境(跳过)
### 2. 安装第一个Cassandra节点，解压到/home/conan/toolkit/cassandra125目录

```{bash}
~ pwd
/home/conan/toolkit/cassandra125
```

ip地址：192.168.1.200

```{bash}
~ ifconfig
eth0      Link encap:Ethernet  HWaddr 08:00:27:90:e8:19
      inet addr:192.168.1.200  Bcast:192.168.1.255  Mask:255.255.255.0
      inet6 addr: fe80::a00:27ff:fe90:e819/64 Scope:Link
      UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
      RX packets:16943 errors:0 dropped:0 overruns:0 frame:0
      TX packets:19527 errors:0 dropped:0 overruns:0 carrier:0
      collisions:0 txqueuelen:1000
      RX bytes:1433046 (1.4 MB)  TX bytes:2902059 (2.9 MB)
```

### 3. 设置环境变更

```{bash}

~ sudo vi /etc/environment
CASSANDRA_HOME=/home/conan/toolkit/cassandra125

~ . /etc/environment

~ export |grep /home/conan/toolkit/cassandra125
declare -x CASSANDRA_HOME="/home/conan/toolkit/cassandra125"
declare -x OLDPWD="/home/conan/toolkit/cassandra125"
declare -x PWD="/home/conan/toolkit/cassandra125/bin"
```

### 4. 创建存储和日志目录

```{bash}
~ sudo rm -rf /var/lib/cassandra

~ sudo mkdir -p /var/lib/cassandra/data
~ sudo mkdir -p /var/lib/cassandra/saved_caches
~ sudo mkdir -p /var/lib/cassandra/commitlog
~ sudo mkdir -p /var/log/cassandra/

~ sudo chown -R conan:conan /var/lib/cassandra
~ sudo chown -R conan:conan /var/log/cassandra

~ ls -l /var/lib/cassandra
drwxr-xr-x  2 conan conan 4096 Jul  4 00:15 commitlog/
drwxr-xr-x  2 conan conan 4096 Jul  4 00:15 data/
drwxr-xr-x  2 conan conan 4096 Jul  4 00:15 saved_caches/
```

### 5. 修改配置文件cassandra.yaml，按文件顺序列表修改的地方

```{bash}

~ vi /home/conan/toolkit/cassandra125/conf/cassandra.yaml

cluster_name: 'case1'
num_tokens: 256

data_file_directories:
- /var/lib/cassandra/data

commitlog_directory: /var/lib/cassandra/commitlog

saved_caches_directory: /var/lib/cassandra/saved_caches

seed_provider:
- class_name: org.apache.cassandra.locator.SimpleSeedProvider
  parameters:
      - seeds: "192.168.1.200"

#listen_address: localhost
listen_address: 192.168.1.200

#rpc_address: localhost
rpc_address: 192.168.1.200

endpoint_snitch: SimpleSnitch
```

### 6. 启动节点

```{bash}
~ cd /home/conan/toolkit/cassandra125/

~ bin/cassandra -f

#部分日志
INFO 00:23:22,785 Enqueuing flush of Memtable-schema_columnfamilies@1792194126(1097/1097 serialized/live bytes, 20 ops)
INFO 00:23:22,786 Writing Memtable-schema_columnfamilies@1792194126(1097/1097 serialized/live bytes, 20 ops)
INFO 00:23:22,796 Completed flushing /var/lib/cassandra/data/system/schema_columnfamilies/system-schema_columnfamilies-ic-2-Data.db (698 bytes) for commitlog position ReplayPosition(segmentId=1372868601408, position=64705)
INFO 00:23:22,797 Enqueuing flush of Memtable-schema_columns@552364977(251/251 serialized/live bytes, 5 ops)
INFO 00:23:22,798 Writing Memtable-schema_columns@552364977(251/251 serialized/live bytes, 5 ops)
INFO 00:23:22,808 Completed flushing /var/lib/cassandra/data/system/schema_columns/system-schema_columns-ic-2-Data.db (209 bytes) for commitlog position ReplayPosition(segmentId=1372868601408, position=64705)
INFO 00:23:22,894 Starting listening for CQL clients on /192.168.1.200:9042...
INFO 00:23:22,906 Binding thrift service to /192.168.1.200:9160
INFO 00:23:22,931 Using TFramedTransport with a max frame size of 15728640 bytes.
INFO 00:23:22,952 Using synchronous/threadpool thrift server on 192.168.1.200 : 9160
INFO 00:23:22,953 Listening for thrift clients...
INFO 00:23:33,101 Created default superuser 'cassandra'
```

### 7. 查看集群的状态

```{bash}
bin/nodetool status
Datacenter: datacenter1
=======================
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address        Load       Tokens  Owns (effective)  Host ID                               Rack
UN  192.168.1.200  51.01 KB   256     100.0%            e7106e0a-1a9e-43a2-9bcc-fc1201076fee  rack1
```

### 8. 增加第2个节点到集群：2个节点

计算机ip: 192.168.1.201

```{bash}

~ ifconfig
eth0      Link encap:Ethernet  HWaddr 08:00:27:0d:0b:0b
          inet addr:192.168.1.201  Bcast:192.168.1.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fe0d:b0b/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:45455 errors:0 dropped:0 overruns:0 frame:0
          TX packets:14717 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:33590582 (33.5 MB)  TX bytes:2549931 (2.5 MB)
```

### 9. 从第一节点192.168.1.200把cassandra125和jdk目录复制过来

```{bash}
~ pwd
/home/conan/toolkit

~ scp -r conan@192.168.1.200:/home/conan/toolkit/cassandra125 .
~ scp -r conan@192.168.1.200:/home/conan/toolkit/jdk16 .

~ ls -l
drwxrwxr-x  9 conan conan 4096 Apr 25 03:04 cassandra125
drwxr-xr-x 10 conan conan 4096 Apr 25 03:33 jdk16
```

### 10. 设置环境变量

```{bash}
~ sudo vi /etc/environment

PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/home/conan/toolkit/jdk16/bin:/home/conan/toolkit/cassandra/bin"
JAVA_HOME=/home/conan/toolkit/jdk16
CASSANDRA_HOME=/home/conan/toolkit/cassandra125

~ . /etc/environment

~ java -version
java version "1.6.0_29"
Java(TM) SE Runtime Environment (build 1.6.0_29-b11)
Java HotSpot(TM) 64-Bit Server VM (build 20.4-b02, mixed mode)
```

### 11. 创建存储和日志目录

```{bash}
~ sudo rm -rf /var/lib/cassandra

~ sudo mkdir -p /var/lib/cassandra/data
~ sudo mkdir -p /var/lib/cassandra/saved_caches
~ sudo mkdir -p /var/lib/cassandra/commitlog
~ sudo mkdir -p /var/log/cassandra/

~ sudo chown -R conan:conan /var/lib/cassandra
~ sudo chown -R conan:conan /var/log/cassandra

~ ls -l /var/lib/cassandra
drwxr-xr-x  2 conan conan 4096 Jul  4 00:15 commitlog/
drwxr-xr-x  2 conan conan 4096 Jul  4 00:15 data/
drwxr-xr-x  2 conan conan 4096 Jul  4 00:15 saved_caches/
```

### 12. 修改配置文件cassandra.yaml，按文件顺序列表修改的地方

```{bash}
~ vi /home/conan/toolkit/cassandra125/conf/cassandra.yaml

cluster_name: 'case1'

seed_provider:
- class_name: org.apache.cassandra.locator.SimpleSeedProvider
  parameters:
      - seeds: "192.168.1.200"

listen_address: 192.168.1.201

rpc_address: 192.168.1.201
```

### 13. 启动节点192.168.1.201

```{bash}
~ bin/cassandra -f

//部分日志
INFO 03:36:47,476 Completed flushing /var/lib/cassandra/data/system/local/system-local-ic-4-Data.db (75 bytes) for commitlog position ReplayPosition(segmentId=1366832174115, position=77582)
INFO 03:36:47,504 Compacting [SSTableReader(path='/var/lib/cassandra/data/system/local/system-local-ic-1-Data.db'), SSTableReader(path='/var/lib/cassandra/data/system/local/system-local-ic-3-Data.db'), SSTableReader(path='/var/lib/cassandra/data/system/local/system-local-ic-4-Data.db'), SSTableReader(path='/var/lib/cassandra/data/system/local/system-local-ic-2-Data.db')]
INFO 03:36:47,527 Enqueuing flush of Memtable-local@692438881(10094/10094 serialized/live bytes, 257 ops)
INFO 03:36:47,533 Writing Memtable-local@692438881(10094/10094 serialized/live bytes, 257 ops)
INFO 03:36:47,547 Completed flushing /var/lib/cassandra/data/system/local/system-local-ic-5-Data.db (5365 bytes) for commitlog position ReplayPosition(segmentId=1366832174115, position=89585)
INFO 03:36:47,660 Node /192.168.1.201 state jump to normal
INFO 03:36:47,663 Startup completed! Now serving reads.
INFO 03:36:47,686 Compacted 4 sstables to [/var/lib/cassandra/data/system/local/system-local-ic-6,].  5,956 bytes to 5,687 (~95% of original) in 158ms = 0.034326MB/s.  4 total rows, 1 unique.  Row merge counts were {1:0, 2:0, 3:0, 4:1, }
INFO 03:36:47,771 Starting listening for CQL clients on /192.168.1.201:9042...
INFO 03:36:47,785 Binding thrift service to /192.168.1.201:9160
INFO 03:36:47,810 Using TFramedTransport with a max frame size of 15728640 bytes.
INFO 03:36:47,834 Using synchronous/threadpool thrift server on 192.168.1.201 : 9160
INFO 03:36:47,834 Listening for thrift clients...
```

### 14. 查看节点1，192.168.1.200的日志

```{bash}
INFO 01:01:27,382 InetAddress /192.168.1.201 is now UP
INFO 01:01:58,660 Beginning transfer to /192.168.1.201
INFO 01:01:58,661 Flushing memtables for [CFS(Keyspace='system_auth', ColumnFamily='users')]...
INFO 01:01:58,663 Enqueuing flush of Memtable-users@1338035062(28/28 serialized/live bytes, 2 ops)
INFO 01:01:58,668 Writing Memtable-users@1338035062(28/28 serialized/live bytes, 2 ops)
INFO 01:01:59,010 Completed flushing /var/lib/cassandra/data/system_auth/users/system_auth-users-ic-1-Data.db (64 bytes) for commitlog position ReplayPosition(segmentId=1372868601408, position=65900)
INFO 01:01:59,047 Stream context metadata [/var/lib/cassandra/data/system_auth/users/system_auth-users-ic-1-Data.db sections=1 progress=0/64 - 0%], 1 sstables.
INFO 01:01:59,048 Streaming to /192.168.1.201
INFO 01:01:59,122 Successfully sent /var/lib/cassandra/data/system_auth/users/system_auth-users-ic-1-Data.db to /192.168.1.201
INFO 01:01:59,123 Finished streaming session to /192.168.1.201
INFO 01:01:59,424 Enqueuing flush of Memtable-peers@1855686378(10279/10279 serialized/live bytes, 271 ops)
INFO 01:01:59,425 Writing Memtable-peers@1855686378(10279/10279 serialized/live bytes, 271 ops)
INFO 01:01:59,497 Completed flushing /var/lib/cassandra/data/system/peers/system-peers-ic-1-Data.db (5538 bytes) for commitlog position ReplayPosition(segmentId=1372868601408, position=77902)
```

### 15. 集群中已经成功加载了192.168.1.201个节点了。

```{bash}
bin/nodetool status

Datacenter: datacenter1
=======================
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address        Load       Tokens  Owns (effective)  Host ID                               Rack
UN  192.168.1.200  65.46 KB   256     48.7%             e7106e0a-1a9e-43a2-9bcc-fc1201076fee  rack1
UN  192.168.1.201  58.04 KB   256     51.3%             8eef1965-9822-44bf-a9f6-fff5b87bc474  rack1
```

**实验完成！！**

## 2. 实验过程的错误及修复

### 1. 不要在已经建立keystore的节点，再修改cluster name

出现下面的错误

```{bash}
ERROR 23:29:58,004 Fatal exception during initialization
org.apache.cassandra.exceptions.ConfigurationException: Saved cluster name Test Cluster != configured name case1
```

解决办法：http://wiki.apache.org/cassandra/FAQ

```{bash}

Cassandra says "ClusterName mismatch: oldClusterName != newClusterName" and refuses to start

To prevent operator errors, Cassandra stores the name of the cluster in its system table. If you need to rename a cluster for some reason, you can:

Perform these steps on each node:

Start the cassandra-cli connected locally to this node.
Run the following:
use system;
set LocationInfo[utf8('L')][utf8('ClusterName')]=utf8('');
exit;
Run nodetool flush on this node.
Update the cassandra.yaml file for the cluster_name as the same as 2b).
Restart the node.
Once all nodes have been had this operation performed and restarted, nodetool ring should show all nodes as UP.
```

### 2. 修改配置文件时:后一定要有空格

会出现下面的错误提示：

```{bash}
while scanning a simple key; could not found expected ':'
```

举例：修改下面配置

```{bash}
#错误语法
listen_address:localhost

#正确语法
listen_address: localhost
```

问题解释：

上面问题是由于，:和”之间没有空格，引起的解析错误。

#### 转载请注明出处：http://blog.fens.me/cassandra-clustor/

