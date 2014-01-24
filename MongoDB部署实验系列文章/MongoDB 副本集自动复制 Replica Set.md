MongoDB 副本集自动复制 Replica Set
==========

#### [MongoDB部署实验系列文章](http://blog.fens.me/series-mongodb/)

MongoDB做为NoSQL数据库，最近几年持续升温，越来越多的企业都开始尝试用MongoDB代替原有Database做一些事情。MongoDB也在集群，分片，复制上也有相当不错的的表现。我通过将做各种MongoDB的部署实验进行介绍。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/mongodb-replica-set/

![MongoDB 副本集自动复制 Replica Set](http://blog.fens.me/wp-content/uploads/2013/05/rs.png)

#### 第二篇 MongoDB 副本集自动复制 Replica Set，分为7个部分

1. 初始化文件目录
2. 启动副本集 Replica Set
3. 模拟 PRIMARY失败，SECONDARY自动切换
4. 修复失败节点
5. 恢复失败节点，补充到SECONDARY
6. 删除一个Replica Set节点
7. 新增加一个Replica Set节点

#### 系统环境介绍：

Ubuntu 12.04. LTS 64bit Server

## 1. 初始化文件目录

```{bash}
~ pwd
/home/conan/dbs

~ mkdir node1 node2 node3
~ ls -l
drwxrwxr-x 2 conan conan 4096 May 31 14:21 node1
drwxrwxr-x 2 conan conan 4096 May 31 14:21 node2
drwxrwxr-x 2 conan conan 4096 May 31 14:21 node3
```

## 2. 启动副本集 Replica Set

### 启动node1,node2,node3

```{bash}
mongod --dbpath /home/conan/dbs/node1 --port 10001 --replSet blort --nojournal --fork --logpath /home/conan/dbs/node1.log
mongod --dbpath /home/conan/dbs/node2 --port 10002 --replSet blort --nojournal --fork --logpath /home/conan/dbs/node2.log
mongod --dbpath /home/conan/dbs/node3 --port 10003 --replSet blort --nojournal --fork --logpath /home/conan/dbs/node3.log
```

### 副本集初始化

```{bash}
~ mongo localhost:10001
MongoDB shell version: 2.4.3
connecting to: localhost:10001/test
> rs.initiate({_id:"blort",members:[
{_id:1,host:"localhost:10001"},
{_id:2,host:"localhost:10002"},
{_id:3,host:"localhost:10003"},
]})
{
"info" : "Config now saved locally. Should come online in about a minute.",
"ok" : 1
}
```

### 查看日志信息:node1变选为PRIMARY,node2,node3分别是2个SECONDARY

```{bash}
Fri May 31 14:26:44.728 [conn2] ******
Fri May 31 14:26:44.728 [conn2] replSet info saving a newer config version to local.system.replset
Fri May 31 14:26:44.741 [conn2] replSet saveConfigLocally done
Fri May 31 14:26:44.741 [conn2] replSet replSetInitiate config now saved locally. Should come online in about a minute.
Fri May 31 14:26:44.741 [conn2] command admin.$cmd command: { replSetInitiate: { _id: "blort", members: [ { _id: 1.0, host: "localhost:10001" }, { _id: 2.0, host: "localhost:10002" }, { _id: 3.0, host: "localhost:10003" } ] } } ntoreturn:1 keyUpdates:0 locks(micros) W:646741 reslen:112 652ms
Fri May 31 14:26:53.682 [rsStart] replSet I am localhost:10001
Fri May 31 14:26:53.682 [rsStart] replSet STARTUP2
Fri May 31 14:26:53.683 [rsHealthPoll] replSet member localhost:10002 is up
Fri May 31 14:26:53.684 [rsHealthPoll] replSet member localhost:10003 is up
Fri May 31 14:26:54.285 [initandlisten] connection accepted from 127.0.0.1:46469 #3 (3 connections now open)
Fri May 31 14:26:54.683 [rsSync] replSet SECONDARY
```

### 用mongo客户端查看设置

node1连接

```{bash}

~ mongo localhost:10001
MongoDB shell version: 2.4.3
connecting to: localhost:10001/test
blort:PRIMARY> rs.status()
{
"set" : "blort",
"date" : ISODate("2013-05-31T06:34:12Z"),
"myState" : 1,
"members" : [
{
"_id" : 1,
"name" : "localhost:10001",
"health" : 1,
"state" : 1,
"stateStr" : "PRIMARY",
"uptime" : 659,
"optime" : {
"t" : 1369981604,
"i" : 1
},
"optimeDate" : ISODate("2013-05-31T06:26:44Z"),
"self" : true
},
{
"_id" : 2,
"name" : "localhost:10002",
"health" : 1,
"state" : 2,
"stateStr" : "SECONDARY",
"uptime" : 439,
"optime" : {
"t" : 1369981604,
"i" : 1
},
"optimeDate" : ISODate("2013-05-31T06:26:44Z"),
"lastHeartbeat" : ISODate("2013-05-31T06:34:11Z"),
"lastHeartbeatRecv" : ISODate("1970-01-01T00:00:00Z"),
"pingMs" : 0,
"syncingTo" : "localhost:10001"
},
{
"_id" : 3,
"name" : "localhost:10003",
"health" : 1,
"state" : 2,
"stateStr" : "SECONDARY",
"uptime" : 439,
"optime" : {
"t" : 1369981604,
"i" : 1
},
"optimeDate" : ISODate("2013-05-31T06:26:44Z"),
"lastHeartbeat" : ISODate("2013-05-31T06:34:11Z"),
"lastHeartbeatRecv" : ISODate("1970-01-01T00:00:00Z"),
"pingMs" : 0,
"syncingTo" : "localhost:10001"
}
],
"ok" : 1
}
```

### node2连接

```{bash}
~ mongo localhost:10002
MongoDB shell version: 2.4.3
connecting to: localhost:10002/test
blort:SECONDARY>
```

### 在primary插入数据：

```{bash}
~ mongo localhost:10001
MongoDB shell version: 2.4.3
connecting to: localhost:10001/test

blort:PRIMARY> show dbs
local 1.078125GB

blort:PRIMARY> use fensme
switched to db fensme

blort:PRIMARY> db.user.insert({uid:10001})
blort:PRIMARY> db.user.find()
{ “_id” : ObjectId(“51a8454813321f05df62a8c8″), “uid” : 10001 }
```

### 连接node2，SECONDARY查询数据

```{bash}
~ mongo localhost:10002
MongoDB shell version: 2.4.3
connecting to: localhost:10002/test

blort:SECONDARY> show dbs
fensme 0.203125GB
local 1.078125GB

blort:SECONDARY> use fensme
switched to db fensme

blort:SECONDARY> show collections
Fri May 31 14:39:22.276 JavaScript execution failed: error: { “$err” : “not master and slaveOk=false”, “code” : 13435 } at src/mongo/shell/query.js:L128

blort:SECONDARY> db.user.find()
error: { “$err” : “not master and slaveOk=false”, “code” : 13435 }
```

提示是不是master/slave的结构，SECONDARY不许允查询。

## 3. 模拟 PRIMARY失败，SECONDARY自动切换

```{bash}
ps -aux|grep mongod
conan 5110 0.4 2.1 1723616 44608 ? Sl 14:23 0:05 mongod --dbpath /home/conan/dbs/node1 --port 10001 --replSet blort --nojournal --fork --logpath /home/conan/dbs/node1.log
conan 5173 0.4 2.2 1693812 45960 ? Sl 14:24 0:04 mongod --dbpath /home/conan/dbs/node2 --port 10002 --replSet blort --nojournal --fork --logpath /home/conan/dbs/node2.log
conan 5218 0.4 2.2 1692800 45548 ? Sl 14:24 0:04 mongod --dbpath /home/conan/dbs/node3 --port 10003 --replSet blort --nojournal --fork --logpath /home/conan/dbs/node3.log


kill -9 5110
ps -aux|grep mongod
conan 5173 0.4 2.2 1703048 46096 ? Sl 14:24 0:04 mongod --dbpath /home/conan/dbs/node2 --port 10002 --replSet blort --nojournal --fork --logpath /home/conan/dbs/node2.log
conan 5218 0.4 2.2 1702036 45696 ? Sl 14:24 0:04 mongod --dbpath /home/conan/dbs/node3 --port 10003 --replSet blort --nojournal --fork --logpath /home/conan/dbs/node3.log
```

### 连接node2，SECONDARY查询节点重新选举

```{bash}

~ mongo localhost:10002
MongoDB shell version: 2.4.3
connecting to: localhost:10002/test

blort:SECONDARY> rs.status()
{
“set” : “blort”,
“date” : ISODate(“2013-05-31T06:42:51Z”),
“myState” : 2,
“syncingTo” : “localhost:10003″,
“members” : [
{
"_id" : 1,
"name" : "localhost:10001",
"health" : 0,
"state" : 8,
"stateStr" : "(not reachable/healthy)",
"uptime" : 0,
"optime" : {
"t" : 1369982280,
"i" : 1
},
"optimeDate" : ISODate("2013-05-31T06:38:00Z"),
"lastHeartbeat" : ISODate("2013-05-31T06:42:51Z"),
"lastHeartbeatRecv" : ISODate("1970-01-01T00:00:00Z"),
"pingMs" : 0
},
{
"_id" : 2,
"name" : "localhost:10002",
"health" : 1,
"state" : 2,
"stateStr" : "SECONDARY",
"uptime" : 1088,
"optime" : {
"t" : 1369982280,
"i" : 1
},
"optimeDate" : ISODate("2013-05-31T06:38:00Z"),
"errmsg" : "syncing to: localhost:10003",
"self" : true
},
{
"_id" : 3,
"name" : "localhost:10003",
"health" : 1,
"state" : 1,
"stateStr" : "PRIMARY",
"uptime" : 946,
"optime" : {
"t" : 1369982280,
"i" : 1
},
"optimeDate" : ISODate("2013-05-31T06:38:00Z"),
"lastHeartbeat" : ISODate("2013-05-31T06:42:51Z"),
"lastHeartbeatRecv" : ISODate("1970-01-01T00:00:00Z"),
"pingMs" : 0,
"syncingTo" : "localhost:10001"
}
],
“ok” : 1
}
```

### 查看结果：

* localhost:10001 ： not reachable/healthy
* localhost:10002： SECONDARY
* localhost:10003： PRIMARY

### 连接node3, localhost:10003查看数据

```{bash}
mongo localhost:10003
MongoDB shell version: 2.4.3
connecting to: localhost:10003/test

blort:PRIMARY> show dbs
fensme 0.203125GB
local 1.078125GB

blort:PRIMARY> use fensme
switched to db fensme

blort:PRIMARY> show collections
system.indexes
user

blort:PRIMARY> db.user.find()
{ “_id” : ObjectId(“51a8454813321f05df62a8c8″), “uid” : 10001 }
```

node1失效后，node3被选为了PRIMARY，数据在node3可以查询到。

## 4. 修复失败节点

直接重启node1，启动失败：

```{bash}

~ mongod --dbpath /home/conan/dbs/node1 --port 10001 --replSet blort --nojournal --fork --logpath /home/conan/dbs/node1_restart.log
MongoDB starting : pid=8544 port=10001 dbpath=/home/conan/dbs/node1 64-bit host=u1
Fri May 31 14:49:37.280 [initandlisten] db version v2.4.3
Fri May 31 14:49:37.280 [initandlisten] git version: fe1743177a5ea03e91e0052fb5e2cb2945f6d95f
Fri May 31 14:49:37.280 [initandlisten] build info: Linux ip-10-2-29-40 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_49
Fri May 31 14:49:37.280 [initandlisten] allocator: tcmalloc
Fri May 31 14:49:37.280 [initandlisten] options: { dbpath: "/home/conan/dbs/node1", fork: true, logpath: "/home/conan/dbs/node1_restart.log", nojournal: true, port: 10001, replSet: "blort" }
**************
Unclean shutdown detected.
Please visit http://dochub.mongodb.org/core/repair for recovery instructions.
*************
Fri May 31 14:49:37.281 [initandlisten] exception in initAndListen: 12596 old lock file, terminating
Fri May 31 14:49:37.281 dbexit:
Fri May 31 14:49:37.281 [initandlisten] shutdown: going to close listening sockets...
Fri May 31 14:49:37.281 [initandlisten] shutdown: going to flush diaglog...
Fri May 31 14:49:37.281 [initandlisten] shutdown: going to close sockets...
Fri May 31 14:49:37.281 [initandlisten] shutdown: waiting for fs preallocator...
Fri May 31 14:49:37.281 [initandlisten] shutdown: closing all files...
Fri May 31 14:49:37.281 [initandlisten] closeAllFiles() finished
Fri May 31 14:49:37.281 dbexit: really exiting now
```

## 5. 恢复失败节点，补充到SECONDARY

```{bash}
mkdir /home/conan/dbs/repair/
mongod --dbpath /home/conan/dbs/node1 --port 10001 --replSet blort --nojournal --fork --logpath /home/conan/dbs/node1_restart_repair.log --repairpath /home/conan/dbs/repair/
about to fork child process, waiting until server is ready for connections.
forked process: 13736
all output going to: /home/conan/dbs/node1_restart_repair.log
child process started successfully, parent exiting
```

修复成功。

### 重新连接node1，查看节点状态

```{bash}

~ mongo localhost:10001
MongoDB shell version: 2.4.3
connecting to: localhost:10001/test

blort:SECONDARY> rs.status()
{
“set” : “blort”,
“date” : ISODate(“2013-05-31T07:04:34Z”),
“myState” : 2,
“syncingTo” : “localhost:10003″,
“members” : [
{
"_id" : 1,
"name" : "localhost:10001",
"health" : 1,
"state" : 2,
"stateStr" : "SECONDARY",
"uptime" : 74,
"optime" : {
"t" : 1369982280,
"i" : 1
},
"optimeDate" : ISODate("2013-05-31T06:38:00Z"),
"errmsg" : "syncing to: localhost:10003",
"self" : true
},
{
"_id" : 2,
"name" : "localhost:10002",
"health" : 1,
"state" : 2,
"stateStr" : "SECONDARY",
"uptime" : 74,
"optime" : {
"t" : 1369982280,
"i" : 1
},
"optimeDate" : ISODate("2013-05-31T06:38:00Z"),
"lastHeartbeat" : ISODate("2013-05-31T07:04:33Z"),
"lastHeartbeatRecv" : ISODate("1970-01-01T00:00:00Z"),
"pingMs" : 0,
"syncingTo" : "localhost:10003"
},
{
"_id" : 3,
"name" : "localhost:10003",
"health" : 1,
"state" : 1,
"stateStr" : "PRIMARY",
"uptime" : 74,
"optime" : {
"t" : 1369982280,
"i" : 1
},
"optimeDate" : ISODate("2013-05-31T06:38:00Z"),
"lastHeartbeat" : ISODate("2013-05-31T07:04:33Z"),
"lastHeartbeatRecv" : ISODate("2013-05-31T07:04:34Z"),
"pingMs" : 0
}
],
“ok” : 1
}
```

### 查看结果：

* localhost:10001： SECONDARY
* localhost:10002： SECONDARY
* localhost:10003： PRIMARY

node1节点已恢复。

## 6. 删除一个Replica Set节点

删除node1

```{bash}

blort:PRIMARY> rs.remove("localhost:10001")
Fri May 31 15:40:21.977 DBClientCursor::init call() failed
Fri May 31 15:40:21.978 JavaScript execution failed: Error: error doing query: failed at src/mongo/shell/query.js:L78
Fri May 31 15:40:21.979 trying reconnect to localhost:10003
Fri May 31 15:40:21.980 reconnect localhost:10003 ok
rs.status()
{
"set" : "blort",
"date" : ISODate("2013-05-31T07:40:23Z"),
"myState" : 1,
"members" : [
{
"_id" : 2,
"name" : "localhost:10002",
"health" : 1,
"state" : 2,
"stateStr" : "SECONDARY",
"uptime" : 2,
"optime" : {
"t" : 1369986021,
"i" : 1
},
"optimeDate" : ISODate("2013-05-31T07:40:21Z"),
"lastHeartbeat" : ISODate("2013-05-31T07:40:21Z"),
"lastHeartbeatRecv" : ISODate("2013-05-31T07:40:23Z"),
"pingMs" : 2,
"lastHeartbeatMessage" : "db exception in producer: 10278 dbclient error communicating with server: localhost:10003",
"syncingTo" : "localhost:10003"
},
{
"_id" : 3,
"name" : "localhost:10003",
"health" : 1,
"state" : 1,
"stateStr" : "PRIMARY",
"uptime" : 4539,
"optime" : {
"t" : 1369986021,
"i" : 1
},
"optimeDate" : ISODate("2013-05-31T07:40:21Z"),
"self" : true
}
],
"ok" : 1
}

```

清空node1，数据文件。

```{bash}
rm -rf node1*
ls -l
drwxrwxr-x 3 conan conan 4096 May 31 14:38 node2/
-rw-rw-r-- 1 conan conan 509277 May 31 15:42 node2.log
drwxrwxr-x 3 conan conan 4096 May 31 14:38 node3/
-rw-rw-r-- 1 conan conan 515918 May 31 15:42 node3.log
drwxrwxr-x 2 conan conan 4096 May 31 15:02 repair/
```

## 7. 新增加一个Replica Set节点

```{bash}
mkdir node1
mongod --dbpath /home/conan/dbs/node1 --port 10001 --replSet blort --nojournal --fork --logpath /home/conan/dbs/node1.log
about to fork child process, waiting until server is ready for connections.
forked process: 15145
all output going to: /home/conan/dbs/node1.log
child process started successfully, parent exiting
```

### 新增加的node1节点

```{bash}
rs.add("localhost:10001")
blort:PRIMARY> rs.status()
{
"set" : "blort",
"date" : ISODate("2013-05-31T07:48:28Z"),
"myState" : 1,
"members" : [
{
"_id" : 2,
"name" : "localhost:10002",
"health" : 1,
"state" : 2,
"stateStr" : "SECONDARY",
"uptime" : 487,
"optime" : {
"t" : 1369986467,
"i" : 1
},
"optimeDate" : ISODate("2013-05-31T07:47:47Z"),
"lastHeartbeat" : ISODate("2013-05-31T07:48:26Z"),
"lastHeartbeatRecv" : ISODate("2013-05-31T07:48:27Z"),
"pingMs" : 0,
"syncingTo" : "localhost:10003"
},
{
"_id" : 3,
"name" : "localhost:10003",
"health" : 1,
"state" : 1,
"stateStr" : "PRIMARY",
"uptime" : 5024,
"optime" : {
"t" : 1369986467,
"i" : 1
},
"optimeDate" : ISODate("2013-05-31T07:47:47Z"),
"self" : true
},
{
"_id" : 4,
"name" : "localhost:10001",
"health" : 1,
"state" : 2,
"stateStr" : "SECONDARY",
"uptime" : 41,
"optime" : {
"t" : 1369986467,
"i" : 1
},
"optimeDate" : ISODate("2013-05-31T07:47:47Z"),
"lastHeartbeat" : ISODate("2013-05-31T07:48:27Z"),
"lastHeartbeatRecv" : ISODate("1970-01-01T00:00:00Z"),
"pingMs" : 0,
"syncingTo" : "localhost:10003"
}
],
"ok" : 1
}
```

### 增加的localhost:10001, 为SECONDARY.

实验完成！

Replica Set 做为FailOver的实现，还是很不错的。

#### 转载请注明：http://blog.fens.me/mongodb-replica-set/


