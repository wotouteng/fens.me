RHadoop实践系列之一:Hadoop环境搭建
================

+ Author：张丹(Conan)
+ Date: 2013-03-21
+ Weibo: @Conan_Z
+ Email: bsspirit@gmail.com
+ Blog: http://www.fens.me/blog

**APPs:**

+ @晒粉丝 [http://www.fens.me](http://www.fens.me/)
+ @每日中国天气 [http://apps.weibo.com/chinaweatherapp](http://apps.weibo.com/chinaweatherapp)

**RHadoop实践系列文章**

RHadoop实践系列文章，包含了R语言与Hadoop结合进行海量数据分析。Hadoop主要用来存储海量数据，R语言完成MapReduce 算法，用来替代Java的MapReduce实现。有了RHadoop可以让广大的R语言爱好者，有更强大的工具处理大数据1G, 10G, 100G, TB, PB。 由于大数据所带来的单机性能问题，可能会一去不复返了。

RHadoop实践是一套系列文章，主要包括”Hadoop环境搭建”，”RHadoop安装与使用”，”R实现MapReduce的算法案 例”，”HBase和rhbase的安装与使用”。对于单独的R语言爱好者，Java爱好者，或者Hadoop爱好者来说，同时具备三种语言知识并不容 易。此文虽为入门文章，但R,Java,Hadoop基础知识还是需要大家提前掌握。

#第一篇 Hadoop环境搭建部分，分为3个章节。

+ Haddop环境准备
+ Hadoop完全分步式集群搭建
+ HDFS测试

##Haddop环境准备

文字说明部分：

首先环境准备，这里我选择了Linux Ubuntu操作系统12.04的64位版本，大家可以根据自己的使用习惯选择顺手的Linux。

但JDK一定要用Oracle SUN官方的版本，请从官网下载，操作系统的自带的OpenJDK会有各种不兼容。JDK请选择1.6.x的版本，JDK1.7版本也会有各种的不兼容情况。
http://www.oracle.com/technetwork/java/javase/downloads/index.html

完全分步式的Hadoop集群，这个选择5台一样配置的虚拟机，通过内网的一个DNS服务器，指定5台虚拟机所对应的域名。
每台虚拟机，1G内存，系统硬盘2G，外接硬盘16G。hadoop会存储在外接硬盘上面。

外接硬盘，需要先进行格式化，然后创建目录，再mount到操作系统，通过修改/etc/fstab配置，系统每次重起都是自动加载外接硬盘。
(如果用户操作系统的硬盘够大，不用外接硬盘，这步可以省略)

接下来，为hadoop集群创建访问账号hadoop，创建访问组hadoop，创建用户目录/home/hadoop，把账号，组和用户目录绑定。

再为hadoop的hdfs创建存储位置/hadoop/conan/data0，给hadoop用户权限。

设置SSH自动登陆，从nn.qa.com虚拟机开始，通过ssh-keygen命令，生成id_rsa.pub，再合并到 authorized_keys的文件。再通过scp把authorized_keys复制到其他的虚拟机。循环生成authorized_keys并合并文件。使得5台虚拟机，都有了相互的SSH自动登陆的配置。

环境准备完成，参考下面代码部分，动手实现。

代码部分：

1. 操作系统Ubuntu 12.04 x64

    ~ uname -a
Linux domU-00-16-3e-00-00-85 3.2.0-23-generic #36-Ubuntu SMP Tue Apr 10 20:39:51 UTC 2012 x86_64 x86_64 x86_64 GNU/Linux

2. JAVA环境

~ java -version
java version "1.6.0_29"
Java(TM) SE Runtime Environment (build 1.6.0_29-b11)
Java HotSpot(TM) 64-Bit Server VM (build 20.4-b02, mixed mode)









