ZooKeeper伪分布式集群安装及使用
===========

[让Hadoop跑在云端系列文章](http://blog.fens.me/series-hadoop-cloud/)，介绍了如何整合虚拟化和Hadoop，让Hadoop集群跑在VPS虚拟主机上，通过云向用户提供存储和计算的服务。

现在硬件越来越便宜，一台非品牌服务器，2颗24核CPU，配48G内存，2T的硬盘，已经降到2万块人民币以下了。这种配置如果简单地放几个web应用，显然是奢侈的浪费。就算是用来实现单节点的hadoop，对计算资源浪费也是非常高的。对于这么高性能的计算机，如何有效利用计算资源，就成为成本控制的一项重要议题了。

通过虚拟化技术，我们可以将一台服务器，拆分成12台VPS，每台2核CPU，4G内存，40G硬盘，并且支持资源重新分配。多么伟大的技术啊！现在我们有了12个节点的hadoop集群， 让Hadoop跑在云端，让世界加速。

#### 关于作者：

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/hadoop-hive-10g/

![ZooKeeper伪分布式集群安装及使用](http://blog.fens.me/wp-content/uploads/2013/08/zookeeper.png)

#### 前言

ZooKeeper是Hadoop家族的一款高性能的分布式协作的产品。在单机中，系统协作大都是进程级的操作。分布式系统中，服务协作都是跨服务器才能完成的。在ZooKeeper之前，我们对于协作服务大都使用消息中间件，随着分布式系统的普及，用消息中间件完成协作，会有大量的程序开发。ZooKeeper直接面向于分布式系统，可以减少我们自己的开发，帮助我们更好完成分布式系统的数据管理问题。

#### 目录

1. zookeeper介绍
2. zookeeper单节点安装
3. zookeeper伪分布式集群安装
4. zookeeper命令行操作
5. Java编程现实命令行操作

## 1. zookeeper介绍

ZooKeeper是一个为分布式应用所设计的分布的、开源的协调服务，它主要是用来解决分布式应用中经常遇到的一些数据管理问题，简化分布式应用协调及其管理的难度，提供高性能的分布式服务。ZooKeeper本身可以以Standalone模式安装运行，不过它的长处在于通过分布式ZooKeeper集群（一个Leader，多个Follower），基于一定的策略来保证ZooKeeper集群的稳定性和可用性，从而实现分布式应用的可靠性。

ZooKeeper是作为分布式协调服务，是不需要依赖于Hadoop的环境，也可以为其他的分布式环境提供服务。

## 2. zookeeper单节点安装Standalones模式

### 系统环境：
+ Linux Ubuntu 12.04.2 LTS 64bit server
+ Java: 1.6.0_29 64-Bit Server VM

```{bash}
~ uname -a
Linux conan 3.5.0-23-generic #35~precise1-Ubuntu SMP Fri Jan 25 17:13:26 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux

~ cat /etc/issue
Ubuntu 12.04.2 LTS \n \l

~ java -version
java version "1.6.0_29"
Java(TM) SE Runtime Environment (build 1.6.0_29-b11)
Java HotSpot(TM) 64-Bit Server VM (build 20.4-b02, mixed mode)
```

### 下载zookeeper

```{bash}
~ mkdir /home/conan/toolkit
~ cd /home/conan/toolkit
~ wget http://apache.dataguru.cn/zookeeper/stable/zookeeper-3.4.5.tar.gz
~ tar xvf zookeeper-3.4.5.tar.gz
~ mv zookeeper-3.4.5 zookeeper345
~ cd zookeeper345
~ ls -l
drwxr-xr-x  2 conan conan    4096 Aug 12 04:34 bin
-rw-r--r--  1 conan conan   75988 Aug 12 04:34 build.xml
-rw-r--r--  1 conan conan   70223 Aug 12 04:34 CHANGES.txt
drwxr-xr-x  2 conan conan    4096 Aug 12 04:34 conf
drwxr-xr-x 10 conan conan    4096 Aug 12 04:34 contrib
drwxr-xr-x  2 conan conan    4096 Aug 12 04:34 dist-maven
drwxr-xr-x  6 conan conan    4096 Aug 12 04:34 docs
-rw-r--r--  1 conan conan    1953 Aug 12 04:34 ivysettings.xml
-rw-r--r--  1 conan conan    3120 Aug 12 04:34 ivy.xml
drwxr-xr-x  4 conan conan    4096 Aug 12 04:34 lib
-rw-r--r--  1 conan conan   11358 Aug 12 04:34 LICENSE.txt
-rw-r--r--  1 conan conan     170 Aug 12 04:34 NOTICE.txt
-rw-r--r--  1 conan conan    1770 Aug 12 04:34 README_packaging.txt
-rw-r--r--  1 conan conan    1585 Aug 12 04:34 README.txt
drwxr-xr-x  5 conan conan    4096 Aug 12 04:34 recipes
drwxr-xr-x  8 conan conan    4096 Aug 12 04:34 src
-rw-r--r--  1 conan conan 1315806 Aug 12 04:34 zookeeper-3.4.5.jar
-rw-r--r--  1 conan conan     833 Aug 12 04:34 zookeeper-3.4.5.jar.asc
-rw-r--r--  1 conan conan      33 Aug 12 04:34 zookeeper-3.4.5.jar.md5
-rw-r--r--  1 conan conan      41 Aug 12 04:34 zookeeper-3.4.5.jar.sha1
```

### 修改配置文件conf/zoo.cfg

```{bash}
~ mkdir /home/conan/zoo/zk0
~ cp conf/zoo_sample.cfg conf/zoo.cfg

~ vi conf/zoo.cfg
ckTime=2000
initLimit=10
syncLimit=5
dataDir=/home/conan/zoo/zk0
clientPort=2181
```

非常简单，我们已经配置好了的zookeeper单节点

### 启动zookeeper

```{bash}
~ bin/zkServer.sh
JMX enabled by default
Using config: /home/conan/zoo/zk0/zookeeper345/bin/../conf/zoo.cfg
Usage: bin/zkServer.sh {start|start-foreground|stop|restart|status|upgrade|print-cmd}
conan@conan:~/zoo/zk0/zookeeper345$ bin/zkServer.sh start
JMX enabled by default
Using config: /home/conan/zoo/zk0/zookeeper345/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED

#zk的服务显示为QuorumPeerMain
~ jps
5321 QuorumPeerMain
5338 Jps

#查看运行状态
~ bin/zkServer.sh status
JMX enabled by default
Using config: /home/conan/zoo/zk0/zookeeper345/bin/../conf/zoo.cfg
Mode: standalone
```

单节点的时，Mode会显示为standalone

### 停止ZooKeeper服务

```{bash}
~ bin/zkServer.sh stop
JMX enabled by default
Using config: /home/conan/zoo/zk0/zookeeper345/bin/../conf/zoo.cfg
Stopping zookeeper ... STOPPED
```

## 3. zookeeper伪分布式集群安装

所谓 “伪分布式集群” 就是在，在一台PC中，启动多个ZooKeeper的实例。“完全分布式集群” 是每台PC，启动一个ZooKeeper实例。

由于我的测试环境PC数量有限，所以在一台PC中，启动3个ZooKeeper的实例。

### 创建环境目录

```{bash}
~ mkdir /home/conan/zoo/zk1
~ mkdir /home/conan/zoo/zk2
~ mkdir /home/conan/zoo/zk3

#新建myid文件
~ echo "1" > /home/conan/zoo/zk1/myid
~ echo "2" > /home/conan/zoo/zk2/myid
~ echo "3" > /home/conan/zoo/zk3/myid
```

### 分别修改配置文件
修改：dataDir,clientPort
增加：集群的实例，server.X，”X”表示每个目录中的myid的值

```{bash}
~ vi /home/conan/toolkit/zookeeper345/conf/zk1.cfg
ckTime=2000
initLimit=10
syncLimit=5
dataDir=/home/conan/zoo/zk1
clientPort=2181
server.1=192.168.1.201:2888:3888
server.2=192.168.1.201:2889:3889
server.3=192.168.1.201:2890:3890

~ vi /home/conan/toolkit/zookeeper345/conf/zk2.cfg
ckTime=2000
initLimit=10
syncLimit=5
dataDir=/home/conan/zoo/zk2
clientPort=2182
server.1=192.168.1.201:2888:3888
server.2=192.168.1.201:2889:3889
server.3=192.168.1.201:2890:3890

~ vi /home/conan/toolkit/zookeeper345/conf/zk3.cfg
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/home/conan/zoo/zk3
clientPort=2183
server.1=192.168.1.201:2888:3888
server.2=192.168.1.201:2889:3889
server.3=192.168.1.201:2890:3890
```

3个节点的ZooKeeper集群配置完成，接下来我们的启动服务。

### 启动集群

```{bash}
~ /home/conan/toolkit/zookeeper345/bin/zkServer.sh start zk1.cfg
~ /home/conan/toolkit/zookeeper345/bin/zkServer.sh start zk2.cfg
~ /home/conan/toolkit/zookeeper345/bin/zkServer.sh start zk3.cfg

~ jps
5422 QuorumPeerMain
5395 QuorumPeerMain
5463 QuorumPeerMain
5494 Jps

#查看节点状态
~ /home/conan/toolkit/zookeeper345/bin/zkServer.sh status zk1.cfg
JMX enabled by default
Using config: /home/conan/toolkit/zookeeper345/bin/../conf/zk1.cfg
Mode: follower

~ /home/conan/toolkit/zookeeper345/bin/zkServer.sh status zk2.cfg
JMX enabled by default
Using config: /home/conan/toolkit/zookeeper345/bin/../conf/zk2.cfg
Mode: leader

~ /home/conan/toolkit/zookeeper345/bin/zkServer.sh status zk3.cfg
JMX enabled by default
Using config: /home/conan/toolkit/zookeeper345/bin/../conf/zk3.cfg
Mode: follower
```

我们可以看到zk2是leader，zk1和zk3是follower

### 查看ZooKeeper物理文件目录结构

```{bash}
~ tree  -L 3 /home/conan/zoo
/home/conan/zoo
├── zk0
├── zk1
│   ├── myid
│   ├── version-2
│   │   ├── acceptedEpoch
│   │   ├── currentEpoch
│   │   ├── log.100000001
│   │   └── snapshot.0
│   └── zookeeper_server.pid
├── zk2
│   ├── myid
│   ├── version-2
│   │   ├── acceptedEpoch
│   │   ├── currentEpoch
│   │   ├── log.100000001
│   │   └── snapshot.0
│   └── zookeeper_server.pid
└── zk3
    ├── myid
    ├── version-2
    │   ├── acceptedEpoch
    │   ├── currentEpoch
    │   ├── log.100000001
    │   └── snapshot.100000000
    └── zookeeper_server.pid

7 directories, 18 files
```

## 4. zookeeper命令行操作

我们通过客户端连接ZooKeeper的集群，我们可以任意的zookeeper是进行连接。

```{bash}
~ /home/conan/toolkit/zookeeper345/bin/zkCli.sh -server 192.168.1.201:2181

Connecting to 192.168.1.201
2013-08-12 05:25:39,260 [myid:] - INFO  [main:Environment@100] - Client environment:zookeeper.version=3.4.5-1392090, built on 09/30/2012 17:52 GMT
2013-08-12 05:25:39,267 [myid:] - INFO  [main:Environment@100] - Client environment:host.name=conan
2013-08-12 05:25:39,269 [myid:] - INFO  [main:Environment@100] - Client environment:java.version=1.6.0_29
2013-08-12 05:25:39,269 [myid:] - INFO  [main:Environment@100] - Client environment:java.vendor=Sun Microsystems Inc.
2013-08-12 05:25:39,270 [myid:] - INFO  [main:Environment@100] - Client environment:java.home=/home/conan/toolkit/jdk16/jre
2013-08-12 05:25:39,270 [myid:] - INFO  [main:Environment@100] - Client environment:java.class.path=/home/conan/toolkit/zookeeper345/bin/../build/classes:/home/conan/toolkit/zookeeper345/bin/../build/lib/*.jar:/home/conan/toolkit/zookeeper345/bin/../lib/slf4j-log4j12-1.6.1.jar:/home/conan/toolkit/zookeeper345/bin/../lib/slf4j-api-1.6.1.jar:/home/conan/toolkit/zookeeper345/bin/../lib/netty-3.2.2.Final.jar:/home/conan/toolkit/zookeeper345/bin/../lib/log4j-1.2.15.jar:/home/conan/toolkit/zookeeper345/bin/../lib/jline-0.9.94.jar:/home/conan/toolkit/zookeeper345/bin/../zookeeper-3.4.5.jar:/home/conan/toolkit/zookeeper345/bin/../src/java/lib/*.jar:/home/conan/toolkit/zookeeper345/bin/../conf:
2013-08-12 05:25:39,271 [myid:] - INFO  [main:Environment@100] - Client environment:java.library.path=/home/conan/toolkit/jdk16/jre/lib/amd64/server:/home/conan/toolkit/jdk16/jre/lib/amd64:/home/conan/toolkit/jdk16/jre/../lib/amd64:/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib
2013-08-12 05:25:39,275 [myid:] - INFO  [main:Environment@100] - Client environment:java.io.tmpdir=/tmp
2013-08-12 05:25:39,276 [myid:] - INFO  [main:Environment@100] - Client environment:java.compiler=
2013-08-12 05:25:39,276 [myid:] - INFO  [main:Environment@100] - Client environment:os.name=Linux
2013-08-12 05:25:39,277 [myid:] - INFO  [main:Environment@100] - Client environment:os.arch=amd64
2013-08-12 05:25:39,281 [myid:] - INFO  [main:Environment@100] - Client environment:os.version=3.5.0-23-generic
2013-08-12 05:25:39,282 [myid:] - INFO  [main:Environment@100] - Client environment:user.name=conan
2013-08-12 05:25:39,282 [myid:] - INFO  [main:Environment@100] - Client environment:user.home=/home/conan
2013-08-12 05:25:39,283 [myid:] - INFO  [main:Environment@100] - Client environment:user.dir=/home/conan/zoo
2013-08-12 05:25:39,284 [myid:] - INFO  [main:ZooKeeper@438] - Initiating client connection, connectString=192.168.1.201 sessionTimeout=30000 watcher=org.apache.zookeeper.ZooKeeperMain$MyWatcher@22ba6c83
Welcome to ZooKeeper!
JLine support is enabled
[zk: 192.168.1.201(CONNECTING) 0] 2013-08-12 05:25:39,336 [myid:] - INFO  [main-SendThread(192.168.1.201:2181):ClientCnxn$SendThread@966] - Opening socket connection to server 192.168.1.201/192.168.1.201:2181. Will not attempt to authenticate using SASL (Unable to locate a login configuration)
2013-08-12 05:25:39,345 [myid:] - INFO  [main-SendThread(192.168.1.201:2181):ClientCnxn$SendThread@849] - Socket connection established to 192.168.1.201/192.168.1.201:2181, initiating session
2013-08-12 05:25:39,384 [myid:] - INFO  [main-SendThread(192.168.1.201:2181):ClientCnxn$SendThread@1207] - Session establishment complete on server 192.168.1.201/192.168.1.201:2181, sessionid = 0x1406f3c1ef90001, negotiated timeout = 30000

WATCHER::

WatchedEvent state:SyncConnected type:None path:null

[zk: 192.168.1.201(CONNECTED) 0]
```

集群已连接，下面我们要使用一下，ZooKeeper的命令行操作。

### 命令行操作
通过help打印命令行帮助

```{bash}
[zk: 192.168.1.201(CONNECTED) 1] help
ZooKeeper -server host:port cmd args
        connect host:port
        get path [watch]
        ls path [watch]
        set path data [version]
        rmr path
        delquota [-n|-b] path
        quit
        printwatches on|off
        create [-s] [-e] path data acl
        stat path [watch]
        close
        ls2 path [watch]
        history
        listquota path
        setAcl path acl
        getAcl path
        sync path
        redo cmdno
        addauth scheme auth
        delete path [version]
        setquota -n|-b val path
```

### ZooKeeper的结构，很像是目录结构，我们看到了像ls这样熟悉的命令。

```{bash}
#ls,查看/目录内容
[zk: 192.168.1.201(CONNECTED) 1] ls /
[zookeeper]

#create,创建一个znode节点
[zk: 192.168.1.201(CONNECTED) 2] create /node conan
Created /node

#ls,再查看/目录
[zk: 192.168.1.201(CONNECTED) 3] ls /
[node, zookeeper]

#get,查看/node的数据信息
[zk: 192.168.1.201(CONNECTED) 4] get /node
conan
cZxid = 0x100000006
ctime = Mon Aug 12 05:32:49 CST 2013
mZxid = 0x100000006
mtime = Mon Aug 12 05:32:49 CST 2013
pZxid = 0x100000006
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 5
numChildren = 0

#set,修改数据
[zk: 192.168.1.201(CONNECTED) 5] set /node fens.me
cZxid = 0x100000006
ctime = Mon Aug 12 05:32:49 CST 2013
mZxid = 0x100000007
mtime = Mon Aug 12 05:34:32 CST 2013
pZxid = 0x100000006
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 7
numChildren = 0

#get,再查看/node的数据信息，已改为fens.me
[zk: 192.168.1.201(CONNECTED) 6] get /node
fens.me
cZxid = 0x100000006
ctime = Mon Aug 12 05:32:49 CST 2013
mZxid = 0x100000007
mtime = Mon Aug 12 05:34:32 CST 2013
pZxid = 0x100000006
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 7
numChildren = 0

#delete,删除/node
[zk: 192.168.1.201(CONNECTED) 7] delete /node
[zk: 192.168.1.201(CONNECTED) 8] ls /
[zookeeper]

#quit,退出客户端连接
[zk: 192.168.1.201(CONNECTED) 19] quit
Quitting...
2013-08-12 05:40:29,304 [myid:] - INFO  [main:ZooKeeper@684] - Session: 0x1406f3c1ef90002 closed
2013-08-12 05:40:29,305 [myid:] - INFO  [main-EventThread:ClientCnxn$EventThread@509] - EventThread shut down
```

## 5. Java编程现实命令行操作

```{bash}

package org.conan.zookeeper.demo;

import java.io.IOException;

import org.apache.zookeeper.CreateMode;
import org.apache.zookeeper.KeeperException;
import org.apache.zookeeper.WatchedEvent;
import org.apache.zookeeper.Watcher;
import org.apache.zookeeper.ZooDefs.Ids;
import org.apache.zookeeper.ZooKeeper;

public class BasicDemo1 {

    public static void main(String[] args) throws IOException, KeeperException, InterruptedException {
        // 创建一个与服务器的连接
        ZooKeeper zk = new ZooKeeper("192.168.1.201:2181", 60000, new Watcher() {
            // 监控所有被触发的事件
            public void process(WatchedEvent event) {
                System.out.println("EVENT:" + event.getType());
            }
        });

        // 查看根节点
        System.out.println("ls / => " + zk.getChildren("/", true));

        // 创建一个目录节点
        if (zk.exists("/node", true) == null) {
            zk.create("/node", "conan".getBytes(), Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
            System.out.println("create /node conan");
            // 查看/node节点数据
            System.out.println("get /node => " + new String(zk.getData("/node", false, null)));
            // 查看根节点
            System.out.println("ls / => " + zk.getChildren("/", true));
        }

        // 创建一个子目录节点
        if (zk.exists("/node/sub1", true) == null) {
            zk.create("/node/sub1", "sub1".getBytes(), Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
            System.out.println("create /node/sub1 sub1");
            // 查看node节点
            System.out.println("ls /node => " + zk.getChildren("/node", true));
        }

        // 修改节点数据
        if (zk.exists("/node", true) != null) {
            zk.setData("/node", "changed".getBytes(), -1);
            // 查看/node节点数据
            System.out.println("get /node => " + new String(zk.getData("/node", false, null)));
        }

        // 删除节点
        if (zk.exists("/node/sub1", true) != null) {
            zk.delete("/node/sub1", -1);
            zk.delete("/node", -1);
            // 查看根节点
            System.out.println("ls / => " + zk.getChildren("/", true));
        }

        // 关闭连接
        zk.close();
    }
}
```

### 运行结果：

```{bash}
2013-08-12 15:33:29,699 [myid:] - INFO  [main:Environment@97] - Client environment:zookeeper.version=3.3.1-942149, built on 05/07/2010 17:14 GMT
2013-08-12 15:33:29,702 [myid:] - INFO  [main:Environment@97] - Client environment:host.name=PC201304202140
2013-08-12 15:33:29,702 [myid:] - INFO  [main:Environment@97] - Client environment:java.version=1.6.0_45
2013-08-12 15:33:29,702 [myid:] - INFO  [main:Environment@97] - Client environment:java.vendor=Sun Microsystems Inc.
2013-08-12 15:33:29,702 [myid:] - INFO  [main:Environment@97] - Client environment:java.home=D:\toolkit\java\jdk6\jre
2013-08-12 15:33:29,703 [myid:] - INFO  [main:Environment@97] - Client environment:java.class.path=D:\workspace\java\zkdemo\target\classes;C:\Users\Administrator\.m2\repository\org\apache\hadoop\zookeeper\3.3.1\zookeeper-3.3.1.jar;C:\Users\Administrator\.m2\repository\log4j\log4j\1.2.15\log4j-1.2.15.jar;C:\Users\Administrator\.m2\repository\javax\mail\mail\1.4\mail-1.4.jar;C:\Users\Administrator\.m2\repository\javax\activation\activation\1.1\activation-1.1.jar;C:\Users\Administrator\.m2\repository\jline\jline\0.9.94\jline-0.9.94.jar;C:\Users\Administrator\.m2\repository\junit\junit\3.8.1\junit-3.8.1.jar
2013-08-12 15:33:29,703 [myid:] - INFO  [main:Environment@97] - Client environment:java.library.path=D:\toolkit\java\jdk6\bin;C:\Windows\Sun\Java\bin;C:\Windows\system32;C:\Windows;D:\toolkit\Rtools\bin;D:\toolkit\Rtools\gcc-4.6.3\bin;C:\Program Files (x86)\Common Files\NetSarang;C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;D:\toolkit\Git\cmd;D:\toolkit\Git\bin;C:\Program Files (x86)\Microsoft SQL Server\100\Tools\Binn\;C:\Program Files\Microsoft SQL Server\100\Tools\Binn\;C:\Program Files\Microsoft SQL Server\100\DTS\Binn\;c:\Program Files (x86)\Common Files\Ulead Systems\MPEG;C:\Program Files (x86)\QuickTime\QTSystem\;D:\toolkit\MiKTex\miktex\bin\x64\;D:\toolkit\sshclient;D:\toolkit\ant19\bin;D:\toolkit\eclipse;D:\toolkit\gradle15\bin;D:\toolkit\java\jdk6\bin;D:\toolkit\maven3\bin;D:\toolkit\mysql56\bin;D:\toolkit\python27;D:\toolkit\putty;C:\Program Files\R\R-3.0.1\bin\x64;D:\toolkit\mongodb243\bin;D:\toolkit\php54;D:\toolkit\nginx140;D:\toolkit\nodejs;D:\toolkit\npm12\bin;D:\toolkit\java\jdk6\jre\bin\server;.
2013-08-12 15:33:29,703 [myid:] - INFO  [main:Environment@97] - Client environment:java.io.tmpdir=C:\Users\ADMINI~1\AppData\Local\Temp\
2013-08-12 15:33:29,704 [myid:] - INFO  [main:Environment@97] - Client environment:java.compiler=
2013-08-12 15:33:29,704 [myid:] - INFO  [main:Environment@97] - Client environment:os.name=Windows 7
2013-08-12 15:33:29,704 [myid:] - INFO  [main:Environment@97] - Client environment:os.arch=amd64
2013-08-12 15:33:29,705 [myid:] - INFO  [main:Environment@97] - Client environment:os.version=6.1
2013-08-12 15:33:29,705 [myid:] - INFO  [main:Environment@97] - Client environment:user.name=Administrator
2013-08-12 15:33:29,705 [myid:] - INFO  [main:Environment@97] - Client environment:user.home=C:\Users\Administrator
2013-08-12 15:33:29,706 [myid:] - INFO  [main:Environment@97] - Client environment:user.dir=D:\workspace\java\zkdemo
2013-08-12 15:33:29,707 [myid:] - INFO  [main:ZooKeeper@373] - Initiating client connection, connectString=192.168.1.201:2181 sessionTimeout=60000 watcher=org.conan.zookeeper.demo.BasicDemo1$1@3dfeca64
2013-08-12 15:33:29,731 [myid:] - INFO  [main-SendThread():ClientCnxn$SendThread@1000] - Opening socket connection to server /192.168.1.201:2181
2013-08-12 15:33:38,736 [myid:] - INFO  [main-SendThread(192.168.1.201:2181):ClientCnxn$SendThread@908] - Socket connection established to 192.168.1.201/192.168.1.201:2181, initiating session
2013-08-12 15:33:38,804 [myid:] - INFO  [main-SendThread(192.168.1.201:2181):ClientCnxn$SendThread@701] - Session establishment complete on server 192.168.1.201/192.168.1.201:2181, sessionid = 0x1406f3c1ef9000d, negotiated timeout = 60000
EVENT:None
ls / => [zookeeper]
EVENT:NodeCreated
EVENT:NodeChildrenChanged
create /node conan
get /node => conan
ls / => [node, zookeeper]
EVENT:NodeCreated
create /node/sub1 sub1
ls /node => [sub1]
EVENT:NodeDataChanged
get /node => changed
EVENT:NodeDeleted
EVENT:NodeChildrenChanged
EVENT:NodeChildrenChanged
ls / => [zookeeper]
2013-08-12 15:33:38,877 [myid:] - INFO  [main:ZooKeeper@538] - Session: 0x1406f3c1ef9000d closed
```

### pom.xml，maven配置文件

```{bash}
<?xml version="1.0" encoding="UTF-8"?>
<project
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"
xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
<modelVersion>4.0.0</modelVersion>
<groupId>org.conan.zookeeper</groupId>
<artifactId>zkdemo</artifactId>
<version>0.0.1</version>
<name>zkdemo</name>
<description>zkdemo</description>

<dependencies>
<dependency>
<groupId>org.apache.hadoop</groupId>
<artifactId>zookeeper</artifactId>
<version>3.3.1</version>
<exclusions>
<exclusion>
<groupId>javax.jms</groupId>
<artifactId>jms</artifactId>
</exclusion>
<exclusion>
<groupId>com.sun.jdmk</groupId>
<artifactId>jmxtools</artifactId>
</exclusion>
<exclusion>
<groupId>com.sun.jmx</groupId>
<artifactId>jmxri</artifactId>
</exclusion>
</exclusions>
</dependency>
</dependencies>
</project>
```

基础讲完，接下来就让我动手管理起，我们自己的分布式系统吧。

#### 转载请注明出处：http://blog.fens.me/hadoop-zookeeper-intro/








