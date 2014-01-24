MongoDB 自动分片 auto sharding
===========

#### [MongoDB部署实验系列文章](http://blog.fens.me/series-mongodb/)

MongoDB做为NoSQL数据库，最近几年持续升温，越来越多的企业都开始尝试用MongoDB代替原有Database做一些事情。MongoDB也在集群，分片，复制上也有相当不错的的表现。我通过将做各种MongoDB的部署实验进行介绍。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/mongodb-shard/

![MongoDB 自动分片 auto sharding](http://blog.fens.me/wp-content/uploads/2013/06/sh.png)

#### 第三篇 MongoDB 自动分片 auto sharding，分为6个部分

1. 初始化文件目录
2. 启动shard节点
3. 配置shard节点
4. 插入数据分片实验
5. 删除主分片
6. 重置主分片

#### 系统环境介绍：

Ubuntu 12.04. LTS 64bit Server

## 1. 初始化文件目录

创建目录

* config1,config2,config3是配置节点
* shard1,shard2,shard3是分片节点

```{bash}

~ pwd
/home/conan/dbs
~ mkdir config1 config2 config3 shard1 shard2 shard3
conan@u1:~/dbs$ ls -l
drwxrwxr-x 3 conan conan 4096 May 31 11:27 config1
drwxrwxr-x 3 conan conan 4096 May 31 11:27 config2
drwxrwxr-x 3 conan conan 4096 May 31 11:27 config3
drwxrwxr-x 3 conan conan 4096 May 31 11:28 shard1
drwxrwxr-x 3 conan conan 4096 May 31 11:29 shard2
drwxrwxr-x 3 conan conan 4096 May 31 11:29 shard3
```

## 2. 启动shard节点

![](http://blog.fens.me/wp-content/uploads/2013/06/shard2.jpg)

### 启动config节点

```{bash}
~ mongod --dbpath /home/conan/dbs/config1 --port 20001 --nojournal --fork --logpath /home/conan/dbs/config1.log
~ mongod --dbpath /home/conan/dbs/config2 --port 20002 --nojournal --fork --logpath /home/conan/dbs/config2.log
~ mongod --dbpath /home/conan/dbs/config3 --port 20003 --nojournal --fork --logpath /home/conan/dbs/config3.log
```

### 启动mongos节点

```{bash}
~ mongos --configdb localhost:20001,localhost:20002,localhost:20003 --port 30001 --fork --logpath /home/conan/dbs/mongos1.log
~ mongos --configdb localhost:20001,localhost:20002,localhost:20003 --port 30002 --fork --logpath /home/conan/dbs/mongos2.log
```

### 启动shard节点

```{bash}
~ mongod --dbpath /home/conan/dbs/shard1 --port 10001 --nojournal --fork --logpath /home/conan/dbs/shard1.log
~ mongod --dbpath /home/conan/dbs/shard2 --port 10002 --nojournal --fork --logpath /home/conan/dbs/shard2.log
~ mongod --dbpath /home/conan/dbs/shard3 --port 10003 --nojournal --fork --logpath /home/conan/dbs/shard3.log
```

### 查看端口

```{bash}
~ netstat -nlt
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address Foreign Address State
tcp 0 0 0.0.0.0:21003 0.0.0.0:* LISTEN
tcp 0 0 0.0.0.0:10001 0.0.0.0:* LISTEN
tcp 0 0 0.0.0.0:30001 0.0.0.0:* LISTEN
tcp 0 0 0.0.0.0:10002 0.0.0.0:* LISTEN
tcp 0 0 0.0.0.0:30002 0.0.0.0:* LISTEN
tcp 0 0 0.0.0.0:10003 0.0.0.0:* LISTEN
tcp 0 0 0.0.0.0:22 0.0.0.0:* LISTEN
tcp 0 0 0.0.0.0:11001 0.0.0.0:* LISTEN
tcp 0 0 0.0.0.0:31001 0.0.0.0:* LISTEN
tcp 0 0 0.0.0.0:11002 0.0.0.0:* LISTEN
tcp 0 0 0.0.0.0:31002 0.0.0.0:* LISTEN
tcp 0 0 0.0.0.0:11003 0.0.0.0:* LISTEN
tcp 0 0 0.0.0.0:20001 0.0.0.0:* LISTEN
tcp 0 0 0.0.0.0:20002 0.0.0.0:* LISTEN
tcp 0 0 0.0.0.0:20003 0.0.0.0:* LISTEN
tcp 0 0 0.0.0.0:21001 0.0.0.0:* LISTEN
tcp 0 0 0.0.0.0:21002 0.0.0.0:* LISTEN
tcp6 0 0 :::22 :::* LISTEN
```

## 3. 配置shard节点

### 连接mongos1，在mongos中添加分片：

```{bash}
~ mongo localhost:30001/admin
MongoDB shell version: 2.4.3
connecting to: localhost:30001/admin
```

```{bash}
mongos> db.runCommand({addshard : "localhost:10001", allowLocal : true})
{ "shardAdded" : "shard0000", "ok" : 1 }
mongos> db.runCommand({addshard : "localhost:10002", allowLocal : true})
{ "shardAdded" : "shard0001", "ok" : 1 }
mongos> db.runCommand({addshard : "localhost:10003", allowLocal : true})
{ "shardAdded" : "shard0002", "ok" : 1 }
```

### 错误的语法：(MongoDB2.0.4已经不支持此语法)

```{bash}
mongos> db.runCommand({addshard : "shard1/localhost:10001,localhost:10002",name:"s1", allowLocal : true})
{
"ok" : 0,
"errmsg" : "couldn't connect to new shard socket exception [CONNECT_ERROR] for shard1/localhost:10001,localhost:10002"
}
```

### 查看分片信息

```{bash}
mongos> db.runCommand({listshards:1})
{
"shards" : [
{
"_id" : "shard0000",
"host" : "localhost:10001"
},
{
"_id" : "shard0001",
"host" : "localhost:10002"
},
{
"_id" : "shard0002",
"host" : "localhost:10003"
}
],
"ok" : 1
}
```

### 启用数据库分片：fensme

```{bash}
mongos> db.runCommand({"enablesharding" : "fensme"})
{ "ok" : 1 }
```

注：一旦enable了个数据库，mongos将会把数据库里的不同数据集放在不同的分片上。只有数据集也被分片，否则一个数据集的所有数据将放在一个分片上。

### 启用数据集分片：fensme.users

```{bash}
mongos> db.runCommand({"shardcollection" : "fensme.users", "key" : {"_id" : 1,"uid":1}})
{ "collectionsharded" : "fensme.users", "ok" : 1 }
```

### 查看分片状态

```{bash}
mongos> db.printShardingStatus()
--- Sharding Status ---
sharding version: {
"_id" : 1,
"version" : 3,
"minCompatibleVersion" : 3,
"currentVersion" : 4,
"clusterId" : ObjectId("51a8d3287034310ad2f6a94e")
}
shards:
{ "_id" : "shard0000", "host" : "localhost:10001" }
{ "_id" : "shard0001", "host" : "localhost:10002" }
{ "_id" : "shard0002", "host" : "localhost:10003" }
databases:
{ "_id" : "admin", "partitioned" : false, "primary" : "config" }
{ "_id" : "fensme", "partitioned" : true, "primary" : "shard0000" }
fensme.users
shard key: { "_id" : 1, "uid" : 1 }
chunks:
shard0000 1
{ "_id" : { "$minKey" : 1 }, "uid" : { "$minKey" : 1 } } -->> { "_id" : { "$maxKey" : 1 }, "uid" : { "$maxKey" : 1 } } on : shard0000 { "t" : 1, "i" : 0 }
```

fensme数据库是支持shard的，主shard是shard0000，对应host是localhost:10001

### 再查看config数据库

```{bash}
> mongos> use config
switched to db config
> mongos> show collections
changelog
chunks
collections
databases
lockpings
locks
mongos
settings
shards
system.indexes
tags
version
mongos> db.shards.find()
{ "_id" : "shard0000", "host" : "localhost:10001" }
{ "_id" : "shard0001", "host" : "localhost:10002" }
{ "_id" : "shard0002", "host" : "localhost:10003" }
mongos> db.chunks.find()
{ "_id" : "fensme.users-_id_MinKey", "lastmod" : { "t" : 1, "i" : 0 }, "lastmodEpoch" : ObjectId("51a81aaa81f2196944ef40fa"), "ns" : "fensme.users", "min" : { "_id" : { "$minKey" : 1 } }, "max" : { "_id" : { "$maxKey" : 1 } }, "shard" : "shard0000" }

```

shards配置成功，分片信息都正确

## 4. 插入数据分片实验

### 向fensme.users插入数据, 批量插入10w条记录

```{bash}

> mongos> use fensme
switched to db fensme
> for(var i=0; i<100000; i++){
db.users.insert({_id:i*1597,uid:i});
}
> mongos> db.users.find()
{ "_id" : 10929279, "uid" : 18307 }
{ "_id" : 0, "uid" : 0 }
{ "_id" : 10929876, "uid" : 18308 }
{ "_id" : 597, "uid" : 1 }
{ "_id" : 10930473, "uid" : 18309 }
{ "_id" : 1194, "uid" : 2 }
{ "_id" : 10931070, "uid" : 18310 }
{ "_id" : 1791, "uid" : 3 }
{ "_id" : 10931667, "uid" : 18311 }
{ "_id" : 2388, "uid" : 4 }
{ "_id" : 10932264, "uid" : 18312 }
{ "_id" : 2985, "uid" : 5 }
{ "_id" : 10932861, "uid" : 18313 }
{ "_id" : 3582, "uid" : 6 }
{ "_id" : 10933458, "uid" : 18314 }
{ "_id" : 4179, "uid" : 7 }
{ "_id" : 10934055, "uid" : 18315 }
{ "_id" : 4776, "uid" : 8 }
{ "_id" : 10934652, "uid" : 18316 }
{ "_id" : 5373, "uid" : 9 }
```

### 查看数据分片存储信息

```{bash}

mongos> db.users.stats()
{
"sharded" : true,
"ns" : "fensme.users",
"count" : 100000,
"numExtents" : 12,
"size" : 3200000,
"storageSize" : 13983744,
"totalIndexSize" : 6647088,
"indexSizes" : {
"_id_" : 2812544,
"_id_1_uid_1" : 3834544
},
"avgObjSize" : 32,
"nindexes" : 2,
"nchunks" : 3,
"shards" : {
"shard0000" : {
"ns" : "fensme.users",
"count" : 18307,
"size" : 585824,
"avgObjSize" : 32,
"storageSize" : 2793472,
"numExtents" : 5,
"nindexes" : 2,
"lastExtentSize" : 2097152,
"paddingFactor" : 1,
"systemFlags" : 1,
"userFlags" : 0,
"totalIndexSize" : 1226400,
"indexSizes" : {
"_id_" : 523264,
"_id_1_uid_1" : 703136
},
"ok" : 1
},
"shard0001" : {
"ns" : "fensme.users",
"count" : 81693,
"size" : 2614176,
"avgObjSize" : 32,
"storageSize" : 11182080,
"numExtents" : 6,
"nindexes" : 2,
"lastExtentSize" : 8388608,
"paddingFactor" : 1,
"systemFlags" : 1,
"userFlags" : 0,
"totalIndexSize" : 5404336,
"indexSizes" : {
"_id_" : 2281104,
"_id_1_uid_1" : 3123232
},
"ok" : 1
},
"shard0002" : {
"ns" : "fensme.users",
"count" : 0,
"size" : 0,
"storageSize" : 8192,
"numExtents" : 1,
"nindexes" : 2,
"lastExtentSize" : 8192,
"paddingFactor" : 1,
"systemFlags" : 1,
"userFlags" : 0,
"totalIndexSize" : 16352,
"indexSizes" : {
"_id_" : 8176,
"_id_1_uid_1" : 8176
},
"ok" : 1
}
},
"ok" : 1
}

```

查看users数据集信息，shard0000上面18307条，shard0001上面81693条，shard0002上面没有数据。分片很不均匀。

### 分别连接到shard1,shard2,shard3查看数据分片存储的情况

#### 连接shard1

```{bash}

mongo localhost:10001
MongoDB shell version: 2.4.3
connecting to: localhost:10001/test
> show dbs
fensme 0.203125GB
local 0.078125GB
> use fensme
switched to db fensme
> show collections
system.indexes
users
> db.users.count()
18307
```

#### 连接shard2, 81693条记录

```{bash}
mongo localhost:10002
MongoDB shell version: 2.4.3
connecting to: localhost:10002/test
> use fensme
switched to db fensme
> db.users.count()
81693
```

#### 连接shard3，无数据

```{bash}
mongo localhost:10003
MongoDB shell version: 2.4.3
connecting to: localhost:10002/test
> use fensme
switched to db fensme
> db.users.count()
0
```

**注：分片数据分配不均匀，应该重新规化分片的key。**

## 5. 删除主分片

### 移除Primary分片shard1，localhost:10001

```{bash}
> use admin
mongos> db.runCommand({"removeshard":"localhost:10001"})
{
"msg" : "draining started successfully",
"state" : "started",
"shard" : "shard0000",
"note" : "you need to drop or movePrimary these databases",
"dbsToMove" : [
"fensme"
],
"ok" : 1
}
```

**提示shard0000是主分片，要手动迁移主分片。**

### 查看分片状态

```{bash}
mongos> db.runCommand({listshards:1})
{
"shards" : [
{
"_id" : "shard0001",
"host" : "localhost:10002"
},
{
"_id" : "shard0002",
"host" : "localhost:10003"
},
{
"_id" : "shard0000",
"draining" : true,
"host" : "localhost:10001"
}
],
"ok" : 1
}
```

### draining为正在迁移过程中。。。。

```{bash}
mongos> db.printShardingStatus()
--- Sharding Status ---
sharding version: {
"_id" : 1,
"version" : 3,
"minCompatibleVersion" : 3,
"currentVersion" : 4,
"clusterId" : ObjectId("51a8d3287034310ad2f6a94e")
}
shards:
{ "_id" : "shard0000", "draining" : true, "host" : "localhost:10001" }
{ "_id" : "shard0001", "host" : "localhost:10002" }
{ "_id" : "shard0002", "host" : "localhost:10003" }
databases:
{ "_id" : "admin", "partitioned" : false, "primary" : "config" }
{ "_id" : "fensme", "partitioned" : true, "primary" : "shard0000" }
fensme.users
shard key: { "_id" : 1, "uid" : 1 }
chunks:
shard0002 1
shard0001 2
{ "_id" : { "$minKey" : 1 }, "uid" : { "$minKey" : 1 } } -->> { "_id" : 0, "uid" : 0 } on shard0002 { "t" : 3, "i" : 0 }
{ "_id" : 0, "uid" : 0 } -->> { "_id" : 10929279, "uid" : 18307 } on : shard0001 { "t" : 4 "i" : 0 }
{ "_id" : 10929279, "uid" : 18307 } -->> { "_id" : { "$maxKey" : 1 }, "uid" : { "$maxKey" 1 } } on : shard0001 { "t" : 2, "i" : 0 }

draining为正在迁移过程中。。。。
```

### 查看db.users的分片存储分布

```{bash}
> use fensme
mongos> db.users.stats()
{
"sharded" : true,
"ns" : "fensme.users",
"count" : 118307,
"numExtents" : 12,
"size" : 3785824,
"storageSize" : 13983744,
"totalIndexSize" : 8854608,
"indexSizes" : {
"_id_" : 3752784,
"_id_1_uid_1" : 5101824
},
"avgObjSize" : 32,
"nindexes" : 2,
"nchunks" : 3,
"shards" : {
"shard0000" : {
"ns" : "fensme.users",
"count" : 18307,
"size" : 585824,
"avgObjSize" : 32,
"storageSize" : 2793472,
"numExtents" : 5,
"nindexes" : 2,
"lastExtentSize" : 2097152,
"paddingFactor" : 1,
"systemFlags" : 1,
"userFlags" : 0,
"totalIndexSize" : 1226400,
"indexSizes" : {
"_id_" : 523264,
"_id_1_uid_1" : 703136
},
"ok" : 1
},
"shard0001" : {
"ns" : "fensme.users",
"count" : 100000,
"size" : 3200000,
"avgObjSize" : 32,
"storageSize" : 11182080,
"numExtents" : 6,
"nindexes" : 2,
"lastExtentSize" : 8388608,
"paddingFactor" : 1,
"systemFlags" : 1,
"userFlags" : 0,
"totalIndexSize" : 7611856,
"indexSizes" : {
"_id_" : 3221344,
"_id_1_uid_1" : 4390512
},
"ok" : 1
},
"shard0002" : {
"ns" : "fensme.users",
"count" : 0,
"size" : 0,
"storageSize" : 8192,
"numExtents" : 1,
"nindexes" : 2,
"lastExtentSize" : 8192,
"paddingFactor" : 1,
"systemFlags" : 1,
"userFlags" : 0,
"totalIndexSize" : 16352,
"indexSizes" : {
"_id_" : 8176,
"_id_1_uid_1" : 8176
},
"ok" : 1
}
},
"ok" : 1
}
```

发现shard0000的数据，都被自动迁移到了shard0001

### 连接node2，发现10W条数据全在这里。

```{bash}
mongo localhost:10002
MongoDB shell version: 2.4.3
connecting to: localhost:10002/test
> use fensme
switched to db fensme
> db.users.count()
100000
```

## 6. 重置主分片

### 重新设置主分片为shard0002

```{bash}
mongos> use admin
switched to db admin
mongos> db.runCommand({"moveprimary":"fensme","to":"localhost:10003"})
{ "primary " : "shard0002:localhost:10003", "ok" : 1 }
```

### 再次删除node1, shard0000

```{bash}
use admin
mongos> db.runCommand({"removeshard":"localhost:10001"})
{
"msg" : "removeshard completed successfully",
"state" : "completed",
"shard" : "shard0000",
"ok" : 1
}
```

删除分片成功。

### 查看分片信息

```{bash}
mongos> db.printShardingStatus()
--- Sharding Status ---
sharding version: {
"_id" : 1,
"version" : 3,
"minCompatibleVersion" : 3,
"currentVersion" : 4,
"clusterId" : ObjectId("51a8e8b8bf1411b5099da477")
}
shards:
{ "_id" : "shard0001", "host" : "localhost:10002" }
{ "_id" : "shard0002", "host" : "localhost:10003" }
databases:
{ "_id" : "admin", "partitioned" : false, "primary" : "config" }
{ "_id" : "fensme", "partitioned" : true, "primary" : "shard0002" }
fensme.users
shard key: { "_id" : 1, "uid" : 1 }
chunks:
shard0002 1
shard0001 2
{ "_id" : { "$minKey" : 1 }, "uid" : { "$minKey" : 1 } } -->> { "_id" : 0, "uid" : 0 } on : shard0002 { "t" : 3, "i" : 0 }
{ "_id" : 0, "uid" : 0 } -->> { "_id" : 29236279, "uid" : 18307 } on : shard0001 { "t" : 4, "i" : 0 }
{ "_id" : 29236279, "uid" : 18307 } -->> { "_id" : { "$maxKey" : 1 }, "uid" : { "$maxKey" : 1 } } on : shard0001 { "t" : 2, "i" : 0 }

```

fensme的数据库的primary分片，成功的改为了shard0002.

### 再看数据的分布情况：

```{bash}
mongos> use fensme
switched to db fensme
mongos> db.users.stats()
{
"sharded" : true,
"ns" : "fensme.users",
"count" : 100000,
"numExtents" : 7,
"size" : 3200000,
"storageSize" : 11190272,
"totalIndexSize" : 7628208,
"indexSizes" : {
"_id_" : 3229520,
"_id_1_uid_1" : 4398688
},
"avgObjSize" : 32,
"nindexes" : 2,
"nchunks" : 3,
"shards" : {
"shard0001" : {
"ns" : "fensme.users",
"count" : 100000,
"size" : 3200000,
"avgObjSize" : 32,
"storageSize" : 11182080,
"numExtents" : 6,
"nindexes" : 2,
"lastExtentSize" : 8388608,
"paddingFactor" : 1,
"systemFlags" : 1,
"userFlags" : 0,
"totalIndexSize" : 7611856,
"indexSizes" : {
"_id_" : 3221344,
"_id_1_uid_1" : 4390512
},
"ok" : 1
},
"shard0002" : {
"ns" : "fensme.users",
"count" : 0,
"size" : 0,
"storageSize" : 8192,
"numExtents" : 1,
"nindexes" : 2,
"lastExtentSize" : 8192,
"paddingFactor" : 1,
"systemFlags" : 1,
"userFlags" : 0,
"totalIndexSize" : 16352,
"indexSizes" : {
"_id_" : 8176,
"_id_1_uid_1" : 8176
},
"ok" : 1
}
},
"ok" : 1
}
```

shard0001包括全部10W条数据，shard0002没有数据，shard0000已经不存储。

**实验完成！！**

#### 转载请注明：http://blog.fens.me/mongodb-shard/




