ZooKeeper实现分布式队列Queue
==============

[让Hadoop跑在云端系列文章](http://blog.fens.me/series-hadoop-cloud/)，介绍了如何整合虚拟化和Hadoop，让Hadoop集群跑在VPS虚拟主机上，通过云向用户提供存储和计算的服务。

现在硬件越来越便宜，一台非品牌服务器，2颗24核CPU，配48G内存，2T的硬盘，已经降到2万块人民币以下了。这种配置如果简单地放几个web应用，显然是奢侈的浪费。就算是用来实现单节点的hadoop，对计算资源浪费也是非常高的。对于这么高性能的计算机，如何有效利用计算资源，就成为成本控制的一项重要议题了。

通过虚拟化技术，我们可以将一台服务器，拆分成12台VPS，每台2核CPU，4G内存，40G硬盘，并且支持资源重新分配。多么伟大的技术啊！现在我们有了12个节点的hadoop集群， 让Hadoop跑在云端，让世界加速。

#### 关于作者：

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/hadoop-hive-10g/

![ZooKeeper实现分布式队列Queue](http://blog.fens.me/wp-content/uploads/2013/08/zookeeper-queue1.png)

#### 前言
ZooKeeper是一个分步式的协作系统，何为协作，ZooKeeper价值又有何体现。通过这篇文章的分布式队列的案例，你将了解到ZooKeeper的强大。关于ZooKeeper的基本使用，请参考：ZooKeeper伪分步式集群安装及使用

#### 目录

1. 分布式队列
2. 设计思路
3. 程序实现

## 1. 分布式队列

队列有很多种产品，大都是消息系统所实现的，像ActiveMQ,JBossMQ,RabbitMQ,IBM-MQ等。分步式队列产品并不太多，像Beanstalkd。

本文实现的分布式对列，是基于ZooKeeper现实的一种同步的分步式队列，当一个队列的成员都聚齐时，这个队列才可用，否则一直等待所有成员到达。

## 2. 设计思路

创建一个父目录 /queue，每个成员都监控(Watch)标志位目录/queue/start 是否存在，然后每个成员都加入这个队列，加入队列的方式就是创建 /queue/x(i)的临时目录节点，然后每个成员获取 /queue 目录的所有目录节点，也就是 x(i)。判断 i 的值是否已经是成员的个数，如果小于成员个数等待 /queue/start 的出现，如果已经相等就创建 /queue/start。

### 产品流程图

![](http://blog.fens.me/wp-content/uploads/2013/08/zookeeper-queue.png)

### 应用实例

![](http://blog.fens.me/wp-content/uploads/2013/08/zookeeper-queue-dataprocess.png)

### 图标解释

1. app1,app2,app3,app4是4个独立的业务系统
2. zk1,zk2,zk3是ZooKeeper集群的3个连接点
3. /queue，是znode的队列，假设队列长度为3
4. /queue/x1，是znode队列中，1号排对者，由app1提交，同步请求，app1挂载等待
5. /queue/x2，是znode队列中，2号排对者，由app2提交，同步请求，app2挂起等待
6. /queue/x3，是znode队列中，3号排对者，由app3提交，同步请求，app3挂起等待
7. /queue/start，当znode队列中满了，触发创建开始节点
8. 当/qeueu/start被创建后，app4被启动，所有zk的连接通知同步程序(红色线)，队列已完成，所有程序结束

### 注：

+ 1). 创建/queue/x1,/queue/x2,/queue/x3没有前后顺序，提交后程序就同步挂起。
+ 2). app1可以通过zk2提交，app2也可通过zk3提交
+ 3). app1可以提交3次请求，生成x1,x2,x3使用队列充满
+ 4). /queue/start被创建后，zk1会监听到这个事件，再告诉app1，队列已完成！

## 3. 程序实现

### 1). 单节点模拟实验

模拟app1，通过zk1，提交3个请求

```{bash}
   public static void doOne() throws Exception {
        String host1 = "192.168.1.201:2181";
        ZooKeeper zk = connection(host1);
        initQueue(zk);
        joinQueue(zk, 1);
        joinQueue(zk, 2);
        joinQueue(zk, 3);
        zk.close();
    }
```

创建一个与服务器的连接

```{bash}
    public static ZooKeeper connection(String host) throws IOException {
        ZooKeeper zk = new ZooKeeper(host, 60000, new Watcher() {
            // 监听/queue/start创建的事件
            public void process(WatchedEvent event) {
                if (event.getPath() != null && event.getPath().equals("/queue/start") && event.getType() == Event.EventType.NodeCreated) {
                    System.out.println("Queue has Completed.Finish testing!!!");
                }
            }
        });
        return zk;
    }
```

出始化队列

```{bash}

    public static void initQueue(ZooKeeper zk) throws KeeperException, InterruptedException {
        System.out.println("WATCH => /queue/start");
        zk.exists("/queue/start", true);

        if (zk.exists("/queue", false) == null) {
            System.out.println("create /queue task-queue");
            zk.create("/queue", "task-queue".getBytes(), Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
        } else {
            System.out.println("/queue is exist!");
        }
    }
```

增加队列节点

```{bash}
    public static void joinQueue(ZooKeeper zk, int x) throws KeeperException, InterruptedException {
        System.out.println("create /queue/x" + x + " x" + x);
        zk.create("/queue/x" + x, ("x" + x).getBytes(), Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL_SEQUENTIAL);
        isCompleted(zk);
    }
```

检查队列是否完整

```{bash}
    public static void isCompleted(ZooKeeper zk) throws KeeperException, InterruptedException {
        int size = 3;
        int length = zk.getChildren("/queue", true).size();

        System.out.println("Queue Complete:" + length + "/" + size);
        if (length >= size) {
            System.out.println("create /queue/start start");
            zk.create("/queue/start", "start".getBytes(), Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL);
        } 
    }
```

启动函数main

```{bash}
public static void main(String[] args) throws Exception {
    doOne();
}
```

运行结果：

```{bash}

WATCH => /queue/start
/queue is exist!
create /queue/x1 x1
Queue Complete:1/3
create /queue/x2 x2
Queue Complete:2/3
create /queue/x3 x3
Queue Complete:3/3
create /queue/start start
Queue has Completed.Finish testing!!!
```

完全符合我的们预期。接下来我们看分布式环境

### 2). 分布式模拟实验

模拟app1通过zk1提交x1,app2通过zk2提交x2,app3通过zk3提交x3

```{bash}
    public static void doAction(int client) throws Exception {
        String host1 = "192.168.1.201:2181";
        String host2 = "192.168.1.201:2182";
        String host3 = "192.168.1.201:2183";

        ZooKeeper zk = null;
        switch (client) {
        case 1:
            zk = connection(host1);
            initQueue(zk);
            joinQueue(zk, 1);
            break;
        case 2:
            zk = connection(host2);
            initQueue(zk);
            joinQueue(zk, 2);
            break;
        case 3:
            zk = connection(host3);
            initQueue(zk);
            joinQueue(zk, 3);
            break;
        }
    }
```

注：

+ 1). 为了简单起见，我们没有增加复杂的多线程控制的机制。
+ 2). 没有调用zk.close()方法，也就是说，app1执行完单独的提交，app1就结束了，但zk1还存在着，所以/queue/x1存在于队列。
+ 3). 程序启动方法，分3次启动，命令行传不同的参数，分别是1,2,3

![](http://blog.fens.me/wp-content/uploads/2013/08/zk-run1.png)

执行app1–>zk1

```{bash}
#日志输出
WATCH => /queue/start
/queue is exist!
create /queue/x1 x1
Queue Complete:1/3

#zookeeper控制台
[zk: 192.168.1.201:2181(CONNECTED) 4] ls /queue
[x10000000011]
```

执行app2–>zk2

```{bash}
#日志输出
WATCH => /queue/start
/queue is exist!
create /queue/x2 x2
Queue Complete:2/3

#zookeeper控制台
[zk: 192.168.1.201:2181(CONNECTED) 5] ls /queue
[x20000000012, x10000000011]
```
执行app3–>zk3

```{bash}
#日志输出
WATCH => /queue/start
/queue is exist!
create /queue/x3 x3
Queue Complete:3/3
create /queue/start start
Queue has Completed.Finish testing!!!

#zookeeper控制台
[zk: 192.168.1.201:2181(CONNECTED) 6] ls /queue
[x30000000016, x10000000014, start, x20000000015]
```

/queue/stats被建立，打印出“Queue has Completed.Finish testing!!!”，代表调用app4完成！

我们完成分布式队列的实验，由于时间仓促。文字说明及代码难免有一些问题，请发现问题的同学帮忙指正。

下面贴一下完整的代码：

```{bash}

package org.conan.zookeeper.demo;

import java.io.IOException;

import org.apache.zookeeper.CreateMode;
import org.apache.zookeeper.KeeperException;
import org.apache.zookeeper.WatchedEvent;
import org.apache.zookeeper.Watcher;
import org.apache.zookeeper.ZooKeeper;
import org.apache.zookeeper.ZooDefs.Ids;

public class QueueZooKeeper {

    public static void main(String[] args) throws Exception {
        if (args.length == 0) {
            doOne();
        } else {
            doAction(Integer.parseInt(args[0]));
        }
    }

    public static void doOne() throws Exception {
        String host1 = "192.168.1.201:2181";
        ZooKeeper zk = connection(host1);
        initQueue(zk);
        joinQueue(zk, 1);
        joinQueue(zk, 2);
        joinQueue(zk, 3);
        zk.close();
    }

    public static void doAction(int client) throws Exception {
        String host1 = "192.168.1.201:2181";
        String host2 = "192.168.1.201:2182";
        String host3 = "192.168.1.201:2183";

        ZooKeeper zk = null;
        switch (client) {
        case 1:
            zk = connection(host1);
            initQueue(zk);
            joinQueue(zk, 1);
            break;
        case 2:
            zk = connection(host2);
            initQueue(zk);
            joinQueue(zk, 2);
            break;
        case 3:
            zk = connection(host3);
            initQueue(zk);
            joinQueue(zk, 3);
            break;
        }
    }

    // 创建一个与服务器的连接
    public static ZooKeeper connection(String host) throws IOException {
        ZooKeeper zk = new ZooKeeper(host, 60000, new Watcher() {
            // 监控所有被触发的事件
            public void process(WatchedEvent event) {
                if (event.getType() == Event.EventType.NodeCreated && event.getPath().equals("/queue/start")) {
                    System.out.println("Queue has Completed.Finish testing!!!");
                }
            }
        });
        return zk;
    }

    public static void initQueue(ZooKeeper zk) throws KeeperException, InterruptedException {
        System.out.println("WATCH => /queue/start");
        zk.exists("/queue/start", true);

        if (zk.exists("/queue", false) == null) {
            System.out.println("create /queue task-queue");
            zk.create("/queue", "task-queue".getBytes(), Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
        } else {
            System.out.println("/queue is exist!");
        }
    }

    public static void joinQueue(ZooKeeper zk, int x) throws KeeperException, InterruptedException {
        System.out.println("create /queue/x" + x + " x" + x);
        zk.create("/queue/x" + x, ("x" + x).getBytes(), Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL_SEQUENTIAL);
        isCompleted(zk);
    }

    public static void isCompleted(ZooKeeper zk) throws KeeperException, InterruptedException {
        int size = 3;
        int length = zk.getChildren("/queue", true).size();

        System.out.println("Queue Complete:" + length + "/" + size);
        if (length >= size) {
            System.out.println("create /queue/start start");
            zk.create("/queue/start", "start".getBytes(), Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL);
        } 
    }

}
```

#### 转载请注明出处：http://blog.fens.me/zookeeper-queue














































