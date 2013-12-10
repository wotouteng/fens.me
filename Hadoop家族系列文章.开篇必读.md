Hadoop家族学习路线图
=======================

[Hadoop家族系列文章](http://blog.fens.me/series-hadoop-family/)，主要介绍Hadoop家族产品，常用的项目包括Hadoop, Hive, Pig, HBase, Sqoop, Mahout, Zookeeper, Avro, Ambari, Chukwa，新增加的项目包括，YARN, Hcatalog, Oozie, Cassandra, Hama, Whirr, Flume, Bigtop, Crunch, Hue等。

从2011年开始，中国进入大数据风起云涌的时代，以Hadoop为代表的家族软件，占据了大数据处理的广阔地盘。开源界及厂商，所有数据软件，无一不向Hadoop靠拢。Hadoop也从小众的高富帅领域，变成了大数据开发的标准。在Hadoop原有技术基础之上，出现了Hadoop家族产品，通过“大数据”概念不断创新，推出科技进步。

作为IT界的开发人员，我们也要跟上节奏，抓住机遇，跟着Hadoop一起雄起！

**关于作者：**

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: [http://blog.fens.me](http://blog.fens.me/)
+ email: bsspirit@gmail.com


**转载请注明出处：**
http://blog.fens.me/hadoop-family-roadmap/

![Hadoop家族学习路线](http://blog.fens.me/wp-content/uploads/2013/09/hadoopFamilyRoadmap.png)


**前言**

使用Hadoop已经有一段时间了，从开始的迷茫，到各种的尝试，到现在组合应用….慢慢地涉及到数据处理的事情，已经离不开hadoop了。Hadoop在大数据领域的成功，更引发了它本身的加速发展。现在Hadoop家族产品，已经达到20个了之多。

有必要对自己的知识做一个整理了，把产品和技术都串起来。不仅能加深印象，更可以对以后的技术方向，技术选型做好基础准备。

本文为“Hadoop家族”开篇，[Hadoop家族学习路线图](http://blog.fens.me/hadoop-family-roadmap/)

**目录**

1.Hadoop家族产品

2.Hadoop家族学习路线图

#1. Hadoop家族产品

截止到2013年，根据cloudera的统计，Hadoop家族产品已经达到20个！
http://blog.cloudera.com/blog/2013/01/apache-hadoop-in-2013-the-state-of-the-platform/

接下来，我把这20个产品，分成了2类。

- 第一类，是我已经掌握的
- 第二类，是TODO准备继续学习的

![Hadoop家族](http://blog.fens.me/wp-content/uploads/2013/09/HadoopFamilySmall.png)

**一句话产品介绍:**

+ [Apache Hadoop: ](http://hadoop.apache.org/)是Apache开源组织的一个分布式计算开源框架，提供了一个分布式文件系统子项目(HDFS)和支持MapReduce分布式计算的软件架构。

+ [Apache Hive: ](http://hive.apache.org/)是基于Hadoop的一个数据仓库工具，可以将结构化的数据文件映射为一张数据库表，通过类SQL语句快速实现简单的MapReduce统计，不必开发专门的MapReduce应用，十分适合数据仓库的统计分析。

+ [Apache Pig:] (http://pig.apache.org/)是一个基于Hadoop的大规模数据分析工具，它提供的SQL-LIKE语言叫Pig Latin，该语言的编译器会把类SQL的数据分析请求转换为一系列经过优化处理的MapReduce运算。

+ [Apache HBase: ](http://hbase.apache.org/)是一个高可靠性、高性能、面向列、可伸缩的分布式存储系统，利用HBase技术可在廉价PC Server上搭建起大规模结构化存储集群。

+ [Apache Sqoop:](http://sqoop.apache.org/)是一个用来将Hadoop和关系型数据库中的数据相互转移的工具，可以将一个关系型数据库（MySQL ,Oracle ,Postgres等）中的数据导进到Hadoop的HDFS中，也可以将HDFS的数据导进到关系型数据库中。

+ [Apache Zookeeper:](http://zookeeper.apache.org/)是一个为分布式应用所设计的分布的、开源的协调服务，它主要是用来解决分布式应用中经常遇到的一些数据管理问题，简化分布式应用协调及其管理的难度，提供高性能的分布式服务

+ [Apache Mahout:](http://mahout.apache.org/)是基于Hadoop的机器学习和数据挖掘的一个分布式框架。Mahout用MapReduce实现了部分数据挖掘算法，解决了并行挖掘的问题。

+ [Apache Cassandra:](http://cassandra.apache.org/)是一套开源分布式NoSQL数据库系统。它最初由Facebook开发，用于储存简单格式数据，集Google BigTable的数据模型与Amazon Dynamo的完全分布式的架构于一身

+ [Apache Avro:](http://avro.apache.org/)是一个数据序列化系统，设计用于支持数据密集型，大批量数据交换的应用。Avro是新的数据序列化格式与传输工具，将逐步取代Hadoop原有的IPC机制

+ [Apache Ambari:](http://incubator.apache.org/ambari/) 是一种基于Web的工具，支持Hadoop集群的供应、管理和监控。

+ 










