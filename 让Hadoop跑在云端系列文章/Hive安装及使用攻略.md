Hive安装及使用攻略
===========
[让Hadoop跑在云端系列文章](http://blog.fens.me/series-hadoop-cloud/)，介绍了如何整合虚拟化和Hadoop，让Hadoop集群跑在VPS虚拟主机上，通过云向用户提供存储和计算的服务。

现在硬件越来越便宜，一台非品牌服务器，2颗24核CPU，配48G内存，2T的硬盘，已经降到2万块人民币以下了。这种配置如果简单地放几个web应用，显然是奢侈的浪费。就算是用来实现单节点的hadoop，对计算资源浪费也是非常高的。对于这么高性能的计算机，如何有效利用计算资源，就成为成本控制的一项重要议题了。

通过虚拟化技术，我们可以将一台服务器，拆分成12台VPS，每台2核CPU，4G内存，40G硬盘，并且支持资源重新分配。多么伟大的技术啊！现在我们有了12个节点的hadoop集群， 让Hadoop跑在云端，让世界加速。

#### 关于作者：

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：
 http://blog.fens.me/hadoop-hive-intro/

![Hive安装及使用攻略](http://blog.fens.me/wp-content/uploads/2013/07/hadoop-hive.png)

#### 前言

Hive是Hadoop一个程序接口，Hive让数据分析人员快速上手，Hive使用了类SQL的语法，Hive让JAVA的世界变得简单而轻巧，Hive让Hadoop普及到了程序员以外的人。

从Hive开始，让分析师们也能玩转大数据。

#### 目录

1. Hive的安装
2. Hive的基本使用:CRUD
3. Hive交互式模式
4. 数据导入
5. 数据导出
6. Hive查询HiveQL
7. Hive视图
8. Hive分区表

## 1. Hive的安装

### 系统环境
装好hadoop的环境后，我们可以把Hive装在namenode机器上(c1)。  
hadoop的环境，请参考：[让Hadoop跑在云端系列文章](http://blog.fens.me/series-hadoop-cloud/)，[RHadoop实践系列之一:Hadoop环境搭建](http://blog.fens.me/rhadoop-hadoop/)

下载: hive-0.9.0.tar.gz  
解压到： /home/cos/toolkit/hive-0.9.0

### hive配置

```{bash}
~ cd /home/cos/toolkit/hive-0.9.0
~ cp hive-default.xml.template hive-site.xml
~ cp hive-log4j.properties.template hive-log4j.properties
```

### 修改hive-site.xml配置文件
把Hive的元数据存储到MySQL中

```{bash}

~ vi conf/hive-site.xml

<property>
<name>javax.jdo.option.ConnectionURL</name>
<value>jdbc:mysql://c1:3306/hive_metadata?createDatabaseIfNotExist=true</value>
<description>JDBC connect string for a JDBC metastore</description>
</property>

<property>
<name>javax.jdo.option.ConnectionDriverName</name>
<value>com.mysql.jdbc.Driver</value>
<description>Driver class name for a JDBC metastore</description>
</property>

<property>
<name>javax.jdo.option.ConnectionUserName</name>
<value>hive</value>
<description>username to use against metastore database</description>
</property>

<property>
<name>javax.jdo.option.ConnectionPassword</name>
<value>hive</value>
<description>password to use against metastore database</description>
</property>

<property>
<name>hive.metastore.warehouse.dir</name>
<value>/user/hive/warehouse</value>
<description>location of default database for the warehouse</description>
</property>
```

### 修改hive-log4j.properties

```{bash}
#log4j.appender.EventCounter=org.apache.hadoop.metrics.jvm.EventCounter
log4j.appender.EventCounter=org.apache.hadoop.log.metrics.EventCounter
```

### 设置环境变量

```{bash}
~ sudo vi /etc/environment

PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/home/cos/toolkit/ant184/bin:/home/cos/toolkit/jdk16/bin:/home/cos/toolkit/maven3/bin:/home/cos/toolkit/hadoop-1.0.3/bin:/home/cos/toolkit/hive-0.9.0/bin"

JAVA_HOME=/home/cos/toolkit/jdk16
ANT_HOME=/home/cos/toolkit/ant184
MAVEN_HOME=/home/cos/toolkit/maven3

HADOOP_HOME=/home/cos/toolkit/hadoop-1.0.3
HIVE_HOME=/home/cos/toolkit/hive-0.9.0

CLASSPATH=/home/cos/toolkit/jdk16/lib/dt.jar:/home/cos/toolkit/jdk16/lib/tools.jar
```

### 在hdfs上面，创建目录

```{bash}
$HADOOP_HOME/bin/hadoop fs -mkidr /tmp
$HADOOP_HOME/bin/hadoop fs -mkidr /user/hive/warehouse
$HADOOP_HOME/bin/hadoop fs -chmod g+w /tmp
$HADOOP_HOME/bin/hadoop fs -chmod g+w /user/hive/warehouse
```

### 在MySQL中创建数据库

```{bash}
create database hive_metadata;
grant all on hive_metadata.* to hive@'%' identified by 'hive';
grant all on hive_metadata.* to hive@localhost identified by 'hive';
ALTER DATABASE hive_metadata CHARACTER SET latin1;
```

### 手动上传mysql的jdbc库到hive/lib

```{bash}
~ ls /home/cos/toolkit/hive-0.9.0/lib
mysql-connector-java-5.1.22-bin.jar
```

### 启动hive

```{bash}
#启动metastore服务
~ bin/hive --service metastore &
Starting Hive Metastore Server

#启动hiveserver服务
~ bin/hive --service hiveserver &
Starting Hive Thrift Server

#启动hive客户端
~ bin/hive shell
Logging initialized using configuration in file:/root/hive-0.9.0/conf/hive-log4j.properties
Hive history file=/tmp/root/hive_job_log_root_201211141845_1864939641.txt

hive> show tables
OK
```

### 查询MySQL数据库中的元数据

```{bash}
~ mysql -uroot -p
mysql> use hive_metadata;
Database changed

mysql> show tables;
+-------------------------+
| Tables_in_hive_metadata |
+-------------------------+
| BUCKETING_COLS          |
| CDS                     |
| COLUMNS_V2              |
| DATABASE_PARAMS         |
| DBS                     |
| IDXS                    |
| INDEX_PARAMS            |
| PARTITIONS              |
| PARTITION_KEYS          |
| PARTITION_KEY_VALS      |
| PARTITION_PARAMS        |
| PART_COL_PRIVS          |
| PART_PRIVS              |
| SDS                     |
| SD_PARAMS               |
| SEQUENCE_TABLE          |
| SERDES                  |
| SERDE_PARAMS            |
| SORT_COLS               |
| TABLE_PARAMS            |
| TBLS                    |
| TBL_COL_PRIVS           |
| TBL_PRIVS               |
+-------------------------+
23 rows in set (0.00 sec)
```

Hive已经成功安装，下面是hive的使用攻略。

## 2. Hive的基本使用

1. 进入hive控制台

```{bash}
~ cd /home/cos/toolkit/hive-0.9.0

~ bin/hive shell
Logging initialized using configuration in file:/home/cos/toolkit/hive-0.9.0/conf/hive-log4j.properties
Hive history file=/tmp/cos/hive_job_log_cos_201307160003_95040367.txt
hive>
```

### 新建表

```{bash}

#创建数据(文本以tab分隔)
~ vi /home/cos/demo/t_hive.txt

16      2       3
61      12      13
41      2       31
17      21      3
71      2       31
1       12      34
11      2       34

#创建新表
hive> CREATE TABLE t_hive (a int, b int, c int) ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t';
OK
Time taken: 0.489 seconds

#导入数据t_hive.txt到t_hive表
hive> LOAD DATA LOCAL INPATH '/home/cos/demo/t_hive.txt' OVERWRITE INTO TABLE t_hive ;
Copying data from file:/home/cos/demo/t_hive.txt
Copying file: file:/home/cos/demo/t_hive.txt
Loading data to table default.t_hive
Deleted hdfs://c1.wtmart.com:9000/user/hive/warehouse/t_hive
OK
Time taken: 0.397 seconds
```

### 查看表和数据

```{bash}
#查看表 
hive> show tables;
OK
t_hive
Time taken: 0.099 seconds

#正则匹配表名
hive>show tables '*t*';
OK
t_hive
Time taken: 0.065 seconds

#查看表数据
hive> select * from t_hive;
OK
16      2       3
61      12      13
41      2       31
17      21      3
71      2       31
1       12      34
11      2       34
Time taken: 0.264 seconds

#查看表结构
hive> desc t_hive;
OK
a       int
b       int
c       int
Time taken: 0.1 seconds
```

### 修改表

```{bash}
#增加一个字段
hive> ALTER TABLE t_hive ADD COLUMNS (new_col String);
OK
Time taken: 0.186 seconds
hive> desc t_hive;
OK
a       int
b       int
c       int
new_col string
Time taken: 0.086 seconds

#重命令表名
~ ALTER TABLE t_hive RENAME TO t_hadoop;
OK
Time taken: 0.45 seconds
hive> show tables;
OK
t_hadoop
Time taken: 0.07 seconds
```

### 删除表

```{bash}

hive> DROP TABLE t_hadoop;
OK
Time taken: 0.767 seconds

hive> show tables;
OK
Time taken: 0.064 seconds
```

## 3. Hive交互式模式

+ quit,exit:  退出交互式shell
+ reset: 重置配置为默认值
+ set <key>=<value> : 修改特定变量的值(如果变量名拼写错误，不会报错)
+ set :  输出用户覆盖的hive配置变量
+ set -v : 输出所有Hadoop和Hive的配置变量
+ add FILE[S] *, add JAR[S] *, add ARCHIVE[S] * : 添加 一个或多个 file, jar, archives到分布式缓存
+ list FILE[S], list JAR[S], list ARCHIVE[S] : 输出已经添加到分布式缓存的资源。
+ list FILE[S] *, list JAR[S] *,list ARCHIVE[S] * : 检查给定的资源是否添加到分布式缓存
+ delete FILE[S] *,delete JAR[S] *,delete ARCHIVE[S] * : 从分布式缓存删除指定的资源
+ ! <command> :  从Hive shell执行一个shell命令
+ dfs <dfs command> :  从Hive shell执行一个dfs命令
+ <query string> : 执行一个Hive 查询，然后输出结果到标准输出
+ source FILE <filepath>:  在CLI里执行一个hive脚本文件

## 4. 数据导入

还以刚才的t_hive为例。

```{bash}
#创建表结构
hive> CREATE TABLE t_hive (a int, b int, c int) ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t';
```

### 从操作本地文件系统加载数据(LOCAL)

```{bash}

hive> LOAD DATA LOCAL INPATH '/home/cos/demo/t_hive.txt' OVERWRITE INTO TABLE t_hive ;
Copying data from file:/home/cos/demo/t_hive.txt
Copying file: file:/home/cos/demo/t_hive.txt
Loading data to table default.t_hive
Deleted hdfs://c1.wtmart.com:9000/user/hive/warehouse/t_hive
OK
Time taken: 0.612 seconds

#在HDFS中查找刚刚导入的数据
~ hadoop fs -cat /user/hive/warehouse/t_hive/t_hive.txt

16      2       3
61      12      13
41      2       31
17      21      3
71      2       31
1       12      34
11      2       34
```

### 从HDFS加载数据

```{bash}
创建表t_hive2
hive> CREATE TABLE t_hive2 (a int, b int, c int) ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t';

#从HDFS加载数据
hive> LOAD DATA INPATH '/user/hive/warehouse/t_hive/t_hive.txt' OVERWRITE INTO TABLE t_hive2;
Loading data to table default.t_hive2
Deleted hdfs://c1.wtmart.com:9000/user/hive/warehouse/t_hive2
OK
Time taken: 0.325 seconds

#查看数据
hive> select * from t_hive2;
OK
16      2       3
61      12      13
41      2       31
17      21      3
71      2       31
1       12      34
11      2       34
Time taken: 0.287 seconds
```

### 从其他表导入数据

```{bash}
hive> INSERT OVERWRITE TABLE t_hive2 SELECT * FROM t_hive ;

Total MapReduce jobs = 2
Launching Job 1 out of 2
Number of reduce tasks is set to 0 since there's no reduce operator
Starting Job = job_201307131407_0002, Tracking URL = http://c1.wtmart.com:50030/jobdetails.jsp?jobid=job_201307131407_0002
Kill Command = /home/cos/toolkit/hadoop-1.0.3/libexec/../bin/hadoop job  -Dmapred.job.tracker=hdfs://c1.wtmart.com:9001 -kill job_201307131407_0002
Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 0
2013-07-16 10:32:41,979 Stage-1 map = 0%,  reduce = 0%
2013-07-16 10:32:48,034 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.03 sec
2013-07-16 10:32:49,050 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.03 sec
2013-07-16 10:32:50,068 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.03 sec
2013-07-16 10:32:51,082 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.03 sec
2013-07-16 10:32:52,093 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.03 sec
2013-07-16 10:32:53,102 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.03 sec
2013-07-16 10:32:54,112 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 1.03 sec
MapReduce Total cumulative CPU time: 1 seconds 30 msec
Ended Job = job_201307131407_0002
Ended Job = -314818888, job is filtered out (removed at runtime).
Moving data to: hdfs://c1.wtmart.com:9000/tmp/hive-cos/hive_2013-07-16_10-32-31_323_5732404975764014154/-ext-10000
Loading data to table default.t_hive2
Deleted hdfs://c1.wtmart.com:9000/user/hive/warehouse/t_hive2
Table default.t_hive2 stats: [num_partitions: 0, num_files: 1, num_rows: 0, total_size: 56, raw_data_size: 0]
7 Rows loaded to t_hive2
MapReduce Jobs Launched:
Job 0: Map: 1   Cumulative CPU: 1.03 sec   HDFS Read: 273 HDFS Write: 56 SUCCESS
Total MapReduce CPU Time Spent: 1 seconds 30 msec
OK
Time taken: 23.227 seconds

hive> select * from t_hive2;
OK
16      2       3
61      12      13
41      2       31
17      21      3
71      2       31
1       12      34
11      2       34
Time taken: 0.134 seconds
```
### 创建表并从其他表导入数据

```{bash}
#删除表
hive> DROP TABLE t_hive;

#创建表并从其他表导入数据
hive> CREATE TABLE t_hive AS SELECT * FROM t_hive2 ;

Total MapReduce jobs = 2
Launching Job 1 out of 2
Number of reduce tasks is set to 0 since there's no reduce operator
Starting Job = job_201307131407_0003, Tracking URL = http://c1.wtmart.com:50030/jobdetails.jsp?jobid=job_201307131407_0003
Kill Command = /home/cos/toolkit/hadoop-1.0.3/libexec/../bin/hadoop job  -Dmapred.job.tracker=hdfs://c1.wtmart.com:9001 -kill job_201307131407_0003
Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 0
2013-07-16 10:36:48,612 Stage-1 map = 0%,  reduce = 0%
2013-07-16 10:36:54,648 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.13 sec
2013-07-16 10:36:55,657 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.13 sec
2013-07-16 10:36:56,666 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.13 sec
2013-07-16 10:36:57,673 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.13 sec
2013-07-16 10:36:58,683 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.13 sec
2013-07-16 10:36:59,691 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 1.13 sec
MapReduce Total cumulative CPU time: 1 seconds 130 msec
Ended Job = job_201307131407_0003
Ended Job = -670956236, job is filtered out (removed at runtime).
Moving data to: hdfs://c1.wtmart.com:9000/tmp/hive-cos/hive_2013-07-16_10-36-39_986_1343249562812540343/-ext-10001
Moving data to: hdfs://c1.wtmart.com:9000/user/hive/warehouse/t_hive
Table default.t_hive stats: [num_partitions: 0, num_files: 1, num_rows: 0, total_size: 56, raw_data_size: 0]
7 Rows loaded to hdfs://c1.wtmart.com:9000/tmp/hive-cos/hive_2013-07-16_10-36-39_986_1343249562812540343/-ext-10000
MapReduce Jobs Launched:
Job 0: Map: 1   Cumulative CPU: 1.13 sec   HDFS Read: 272 HDFS Write: 56 SUCCESS
Total MapReduce CPU Time Spent: 1 seconds 130 msec
OK
Time taken: 20.13 seconds

hive> select * from t_hive;
OK
16      2       3
61      12      13
41      2       31
17      21      3
71      2       31
1       12      34
11      2       34
Time taken: 0.109 seconds
```

### 仅复制表结构不导数据

```{bash}
hive> CREATE TABLE t_hive3 LIKE t_hive;
hive> select * from t_hive3;
OK
Time taken: 0.077 seconds
```

### 从MySQL数据库导入数据
我们将在介绍Sqoop时讲。

## 5. 数据导出

### 从HDFS复制到HDFS其他位置

```{bash}
~ hadoop fs -cp /user/hive/warehouse/t_hive /

~ hadoop fs -ls /t_hive
Found 1 items
-rw-r--r--   1 cos supergroup         56 2013-07-16 10:41 /t_hive/000000_0

~ hadoop fs -cat /t_hive/000000_0
1623
611213
41231
17213
71231
11234
11234
```
### 通过Hive导出到本地文件系统

```{bash}
hive> INSERT OVERWRITE LOCAL DIRECTORY '/tmp/t_hive' SELECT * FROM t_hive;
Total MapReduce jobs = 1
Launching Job 1 out of 1
Number of reduce tasks is set to 0 since there's no reduce operator
Starting Job = job_201307131407_0005, Tracking URL = http://c1.wtmart.com:50030/jobdetails.jsp?jobid=job_201307131407_0005
Kill Command = /home/cos/toolkit/hadoop-1.0.3/libexec/../bin/hadoop job  -Dmapred.job.tracker=hdfs://c1.wtmart.com:9001 -kill job_201307131407_0005
Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 0
2013-07-16 10:46:24,774 Stage-1 map = 0%,  reduce = 0%
2013-07-16 10:46:30,823 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 0.87 sec
2013-07-16 10:46:31,833 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 0.87 sec
2013-07-16 10:46:32,844 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 0.87 sec
2013-07-16 10:46:33,856 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 0.87 sec
2013-07-16 10:46:34,865 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 0.87 sec
2013-07-16 10:46:35,873 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 0.87 sec
2013-07-16 10:46:36,884 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 0.87 sec
MapReduce Total cumulative CPU time: 870 msec
Ended Job = job_201307131407_0005
Copying data to local directory /tmp/t_hive
Copying data to local directory /tmp/t_hive
7 Rows loaded to /tmp/t_hive
MapReduce Jobs Launched:
Job 0: Map: 1   Cumulative CPU: 0.87 sec   HDFS Read: 271 HDFS Write: 56 SUCCESS
Total MapReduce CPU Time Spent: 870 msec
OK
Time taken: 23.369 seconds

#查看本地操作系统
hive> ! cat /tmp/t_hive/000000_0;
hive> 1623
611213
41231
17213
71231
11234
11234
```

## 6. Hive查询HiveQL

注：以下代码将去掉map,reduce的日志输出部分。

### 普通查询：排序，列别名，嵌套子查询

```{bash}
hive> FROM (
    >   SELECT b,c as c2 FROM t_hive
    > ) t
    > SELECT t.b, t.c2
    > WHERE b>2
    > LIMIT 2;
12      13
21      3
```

### 连接查询：JOIN

```{bash}
hive> SELECT t1.a,t1.b,t2.a,t2.b
    > FROM t_hive t1 JOIN t_hive2 t2 on t1.a=t2.a
    > WHERE t1.c>10;

1       12      1       12
11      2       11      2
41      2       41      2
61      12      61      12
71      2       71      2
```

### 聚合查询1：count, avg

```{bash}
hive> SELECT count(*), avg(a) FROM t_hive;
7       31.142857142857142
```

### 聚合查询2：count, distinct

```{bash}
hive> SELECT count(DISTINCT b) FROM t_hive;
3
```

### 聚合查询3：GROUP BY, HAVING

```{bash}
#GROUP BY
hive> SELECT avg(a),b,sum(c) FROM t_hive GROUP BY b,c
16.0    2       3
56.0    2       62
11.0    2       34
61.0    12      13
1.0     12      34
17.0    21      3

#HAVING
hive> SELECT avg(a),b,sum(c) FROM t_hive GROUP BY b,c HAVING sum(c)>30
56.0    2       62
11.0    2       34
1.0     12      34
```

## 7. Hive视图

Hive视图和数据库视图的概念是一样的，我们还以t_hive为例。

```{bash}
hive> CREATE VIEW v_hive AS SELECT a,b FROM t_hive where c>30;
hive> select * from v_hive;
41      2
71      2
1       12
11      2
```

### 删除视图

```{bash}
hive> DROP VIEW IF EXISTS v_hive;
OK
Time taken: 0.495 seconds
```

## 8. Hive分区表

分区表是数据库的基本概念，但很多时候数据量不大，我们完全用不到分区表。Hive是一种OLAP数据仓库软件，涉及的数据量是非常大的，所以分区表在这个场景就显得非常重要！！

下面我们重新定义一个数据表结构：t_hft

### 创建数据

```{bash}

~ vi /home/cos/demo/t_hft_20130627.csv
000001,092023,9.76
000002,091947,8.99
000004,092002,9.79
000005,091514,2.2
000001,092008,9.70
000001,092059,9.45

~ vi /home/cos/demo/t_hft_20130628.csv
000001,092023,9.76
000002,091947,8.99
000004,092002,9.79
000005,091514,2.2
000001,092008,9.70
000001,092059,9.45
```

### 创建数据表

```{bash}
DROP TABLE IF EXISTS t_hft;
CREATE TABLE t_hft(
SecurityID STRING,
tradeTime STRING,
PreClosePx DOUBLE
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',';
```

### 创建分区数据表
根据业务：按天和股票ID进行分区设计

```{bash}
DROP TABLE IF EXISTS t_hft;
CREATE TABLE t_hft(
SecurityID STRING,
tradeTime STRING,
PreClosePx DOUBLE
) PARTITIONED BY (tradeDate INT)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',';
```

### 导入数据

```{bash}
#20130627
hive> LOAD DATA LOCAL INPATH '/home/cos/demo/t_hft_20130627.csv' OVERWRITE INTO TABLE t_hft PARTITION (tradeDate=20130627);
Copying data from file:/home/cos/demo/t_hft_20130627.csv
Copying file: file:/home/cos/demo/t_hft_20130627.csv
Loading data to table default.t_hft partition (tradedate=20130627)

#20130628
hive> LOAD DATA LOCAL INPATH '/home/cos/demo/t_hft_20130628.csv' OVERWRITE INTO TABLE t_hft PARTITION (tradeDate=20130628);
Copying data from file:/home/cos/demo/t_hft_20130628.csv
Copying file: file:/home/cos/demo/t_hft_20130628.csv
Loading data to table default.t_hft partition (tradedate=20130628)
```

### 查看分区表

```{bash}
hive> SHOW PARTITIONS t_hft;
tradedate=20130627
tradedate=20130628
Time taken: 0.082 seconds
```

### 查询数据

```{bash}
hive> select * from t_hft where securityid='000001';
000001  092023  9.76    20130627
000001  092008  9.7     20130627
000001  092059  9.45    20130627
000001  092023  9.76    20130628
000001  092008  9.7     20130628
000001  092059  9.45    20130628

hive> select * from t_hft where tradedate=20130627 and PreClosePx<9;
000002  091947  8.99    20130627
000005  091514  2.2     20130627
```

Hive基于使用完成，这些都是日常的操作。后面我会继续讲一下，HiveQL优化及Hive的运维。

#### 转载请注明出处：
http://blog.fens.me/hadoop-hive-intro/












































