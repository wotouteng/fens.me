让Hadoop跑在云端系列文章
================

![](http://blog.fens.me/wp-content/uploads/2013/07/vhadoop.png)

[让Hadoop跑在云端系列文章](http://blog.fens.me/series-hadoop-cloud/)，介绍了如何整合虚拟化和Hadoop，让Hadoop集群跑在VPS虚拟主机上，通过云向用户提供存储和计算的服务。

现在硬件越来越便宜，一台非品牌服务器，2颗24核CPU，配48G内存，2T的硬盘，已经降到2万块人民币以下了。这种配置如果简单地放几个web应用，显然是奢侈的浪费。就算是用来实现单节点的hadoop，对计算资源浪费也是非常高的。对于这么高性能的计算机，如何有效利用计算资源，就成为成本控制的一项重要议题了。

通过虚拟化技术，我们可以将一台服务器，拆分成12台VPS，每台2核CPU，4G内存，40G硬盘，并且支持资源重新分配。多么伟大的技术啊！现在我们有了12个节点的hadoop集群， 让Hadoop跑在云端，让世界加速。

[创建Hadoop母体虚拟机](http://blog.fens.me/hadoop-base-kvm/)

[克隆虚拟机增加Hadoop节点](http://blog.fens.me/hadoop-clone-node/)

[克隆虚拟机优化方案1-安装和配置](http://blog.fens.me/hadoop-clone-improve/)

[增加删除hadoop节点](http://blog.fens.me/hadoop-clone-add-delete/)

[Hive安装及使用攻略](http://blog.fens.me/hadoop-hive-intro/)

[Hive导入10G数据的测试](http://blog.fens.me/hadoop-hive-10g/)

[ZooKeeper伪分步式集群安装及使用](http://blog.fens.me/hadoop-zookeeper-intro/)

[ZooKeeper实现分布式队列Queue](http://blog.fens.me/zookeeper-queue/)

[ZooKeeper实现分布式FIFO队列](http://blog.fens.me/zookeeper-queue-fifo/)





































