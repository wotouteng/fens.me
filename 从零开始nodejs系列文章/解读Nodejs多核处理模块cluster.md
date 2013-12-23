解读Nodejs多核处理模块cluster
============

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-core-cluster/

![解读Nodejs多核处理模块cluster](http://blog.fens.me/wp-content/uploads/2013/09/nodejs-cluster.png)

#### 前言
大家都知道nodejs是一个单进程单线程的服务器引擎，不管有多么的强大硬件，只能利用到单个CPU进行计算。所以，有人开发了第三方的cluster，让node可以利用多核CPU实现并行。

随着nodejs的发展，让nodejs上生产环境，就必须是支持多进程多核处理！在V0.6.0版本，Nodejs内置了cluster的特性。自此，Nodejs终于可以作为一个独立的应用开发解决方案，映入大家眼帘了。

#### 目录

1. cluster介绍
2. cluster的简单使用
3. cluster的工作原理
4. cluster的API
5. master和worker的通信
6. 用cluster实现负载均衡(Load Balance) — win7失败
7. 用cluster实现负载均衡(Load Balance) — ubuntu成功
8. cluster负载均衡策略的测试

## 1. cluster介绍

cluster是一个nodejs内置的模块，用于nodejs多核处理。cluster模块，可以帮助我们简化多进程并行化程序的开发难度，轻松构建一个用于负载均衡的集群。

## 2. cluster的简单使用

### 我的系统环境

+ win7 64bit
+ Nodejs:v0.10.5
+ Npm:1.2.19

在win的环境中，我们通过cluster启动多核的node提供web服务。

新建工程目录：

```{bash}
~ D:\workspace\javascript>mkdir nodejs-cluster && cd nodejs-cluster
```

新建文件：app.js

```{bash}

~ vi app.js

var cluster = require('cluster');
var http = require('http');
var numCPUs = require('os').cpus().length;

if (cluster.isMaster) {
    console.log("master start...");

    // Fork workers.
    for (var i = 0; i < numCPUs; i++) {
        cluster.fork();
    }

    cluster.on('listening',function(worker,address){
        console.log('listening: worker ' + worker.process.pid +', Address: '+address.address+":"+address.port);
    });

    cluster.on('exit', function(worker, code, signal) {
        console.log('worker ' + worker.process.pid + ' died');
    });
} else {
    http.createServer(function(req, res) {
        res.writeHead(200);
        res.end("hello world\n");
    }).listen(0);
}
```

在控制台启动node程序

```{bash}
~ D:\workspace\javascript\nodejs-cluster>node app.js

master start...
listening: worker 2368, Address: 0.0.0.0:57132
listening: worker 1880, Address: 0.0.0.0:57132
listening: worker 1384, Address: 0.0.0.0:57132
listening: worker 1652, Address: 0.0.0.0:57132
```

master是总控节点，worker是运行节点。然后根据CPU的数量，启动worker。我本地是双核双通道的CPU，所以被检测为4核，启动了4个worker。

## 3. cluster的工作原理

每个worker进程通过使用child_process.fork()函数，基于IPC（Inter-Process Communication，进程间通信），实现与master进程间通信。

当worker使用server.listen（...）函数时 ，将参数序列传递给master进程。如果master进程已经匹配workers，会将传递句柄给工人。如果master没有匹配好worker，那么会创建一个worker，再传递并句柄传递给worker。

在边界条件，有3个有趣的行为：
注：下面server.listen()，是对底层“http.Server-->net.Server”类的调用。

1. server.listen({fd: 7}):在master和worker通信过程，通过传递文件，master会监听“文件描述为7”，而不是传递“文件描述为7”的引用。
2. server.listen(handle):master和worker通信过程，通过handle函数进行通信，而不用进程联系
3. server.listen(0):在master和worker通信过程，集群中的worker会打开一个随机端口共用，通过socket通信，像上例中的57132
当多个进程都在 accept() 同样的资源的时候，操作系统的负载均衡非常高效。Node.js没有路由逻辑，worker之间没有共享状态。所以，程序要设计得简单一些，比如基于内存的session。

因为workers都是独力运行的，根据程序的需要，它们可以被独立删除或者重启，worker并不相互影响。只要还有workers存活，则master将继续接收连接。Node不会自动维护workers的数目。我们可以建立自己的连接池。

## 4. cluster的API

官网地址：http://nodejs.org/api/cluster.html#cluster_cluster

### cluster对象

cluster的各种属性和函数


* cluster.setttings:配置集群参数对象
* cluster.isMaster:判断是不是master节点
* cluster.isWorker:判断是不是worker节点
* Event: 'fork': 监听创建worker进程事件
* Event: 'online': 监听worker创建成功事件
* Event: 'listening': 监听worker向master状态事件
* Event: 'disconnect': 监听worker断线事件
* Event: 'exit': 监听worker退出事件
* Event: 'setup': 监听setupMaster事件
* cluster.setupMaster([settings]): 设置集群参数
* cluster.fork([env]): 创建worker进程
* cluster.disconnect([callback]): 关闭worket进程
* cluster.worker: 获得当前的worker对象
* cluster.workers: 获得集群中所有存活的worker对象

### worker对象

worker的各种属性和函数：可以通过cluster.workers, cluster.worket获得。

* worker.id: 进程ID号
* worker.process: ChildProcess对象
* worker.suicide: 在disconnect()后，判断worker是否自杀
* worker.send(message, [sendHandle]): master给worker发送消息。注：worker给发master发送消息要用process.send(message)
* worker.kill([signal='SIGTERM']): 杀死指定的worker，别名destory()
* worker.disconnect(): 断开worker连接，让worker自杀
* Event: 'message': 监听master和worker的message事件
* Event: 'online': 监听指定的worker创建成功事件
* Event: 'listening': 监听master向worker状态事件
* Event: 'disconnect': 监听worker断线事件
* Event: 'exit': 监听worker退出事件

## 5. master和worker的通信

实现cluster的API，让master和worker相互通信。

新建文件: cluster.js

```{bash}
~ vi cluster.js

var cluster = require('cluster');
var http = require('http');
var numCPUs = require('os').cpus().length;

if (cluster.isMaster) {
    console.log('[master] ' + "start master...");

    for (var i = 0; i < numCPUs; i++) {
        var wk = cluster.fork();
        wk.send('[master] ' + 'hi worker' + wk.id);
    }

    cluster.on('fork', function (worker) {
        console.log('[master] ' + 'fork: worker' + worker.id);
    });

    cluster.on('online', function (worker) {
        console.log('[master] ' + 'online: worker' + worker.id);
    });

    cluster.on('listening', function (worker, address) {
        console.log('[master] ' + 'listening: worker' + worker.id + ',pid:' + worker.process.pid + ', Address:' + address.address + ":" + address.port);
    });

    cluster.on('disconnect', function (worker) {
        console.log('[master] ' + 'disconnect: worker' + worker.id);
    });

    cluster.on('exit', function (worker, code, signal) {
        console.log('[master] ' + 'exit worker' + worker.id + ' died');
    });

    function eachWorker(callback) {
        for (var id in cluster.workers) {
            callback(cluster.workers[id]);
        }
    }

    setTimeout(function () {
        eachWorker(function (worker) {
            worker.send('[master] ' + 'send message to worker' + worker.id);
        });
    }, 3000);

    Object.keys(cluster.workers).forEach(function(id) {
        cluster.workers[id].on('message', function(msg){
            console.log('[master] ' + 'message ' + msg);
        });
    });

} else if (cluster.isWorker) {
    console.log('[worker] ' + "start worker ..." + cluster.worker.id);

    process.on('message', function(msg) {
        console.log('[worker] '+msg);
        process.send('[worker] worker'+cluster.worker.id+' received!');
    });

    http.createServer(function (req, res) {
            res.writeHead(200, {"content-type": "text/html"});
            res.end('worker'+cluster.worker.id+',PID:'+process.pid);
    }).listen(3000);

}
```

控制台日志：

```{bash}
~ D:\workspace\javascript\nodejs-cluster>node cluster.js

[master] start master...
[worker] start worker ...1
[worker] [master] hi worker1
[worker] start worker ...2
[worker] [master] hi worker2
[master] fork: worker1
[master] fork: worker2
[master] fork: worker3
[master] fork: worker4
[master] online: worker1
[master] online: worker2
[master] message [worker] worker1 received!
[master] message [worker] worker2 received!
[master] listening: worker1,pid:6068, Address:0.0.0.0:3000
[master] listening: worker2,pid:1408, Address:0.0.0.0:3000
[master] online: worker3
[worker] start worker ...3
[worker] [master] hi worker3
[master] message [worker] worker3 received!
[master] listening: worker3,pid:3428, Address:0.0.0.0:3000
[master] online: worker4
[worker] start worker ...4
[worker] [master] hi worker4
[master] message [worker] worker4 received!
[master] listening: worker4,pid:6872, Address:0.0.0.0:3000
[worker] [master] send message to worker1
[worker] [master] send message to worker2
[worker] [master] send message to worker3
[worker] [master] send message to worker4
[master] message [worker] worker1 received!
[master] message [worker] worker2 received!
[master] message [worker] worker3 received!
[master] message [worker] worker4 received!
```

## 6. 用cluster实现负载均衡(Load Balance) -- win7失败

新建文件: server.js

```{bash}
~ vi server.js

var cluster = require('cluster');
var http = require('http');
var numCPUs = require('os').cpus().length;

if (cluster.isMaster) {
    console.log('[master] ' + "start master...");

    for (var i = 0; i < numCPUs; i++) {
         cluster.fork();
    }

    cluster.on('listening', function (worker, address) {
        console.log('[master] ' + 'listening: worker' + worker.id + ',pid:' + worker.process.pid + ', Address:' + address.address + ":" + address.port);
    });

} else if (cluster.isWorker) {
    console.log('[worker] ' + "start worker ..." + cluster.worker.id);
    http.createServer(function (req, res) {
        console.log('worker'+cluster.worker.id);
        res.end('worker'+cluster.worker.id+',PID:'+process.pid);
    }).listen(3000);
}
```

启动服务器：

```{bash}

~ D:\workspace\javascript\nodejs-cluster>node server.js
[master] start master...
[worker] start worker ...1
[worker] start worker ...2
[master] listening: worker1,pid:1536, Address:0.0.0.0:3000
[master] listening: worker2,pid:5920, Address:0.0.0.0:3000
[worker] start worker ...3
[master] listening: worker3,pid:7156, Address:0.0.0.0:3000
[worker] start worker ...4
[master] listening: worker4,pid:2868, Address:0.0.0.0:3000
worker4
worker4
worker4
worker4
worker4
worker4
worker4
worker4
```

用curl工具访问

```{bash}
C:\Users\Administrator>curl localhost:3000
worker4,PID:2868
C:\Users\Administrator>curl localhost:3000
worker4,PID:2868
C:\Users\Administrator>curl localhost:3000
worker4,PID:2868
C:\Users\Administrator>curl localhost:3000
worker4,PID:2868
C:\Users\Administrator>curl localhost:3000
worker4,PID:2868
C:\Users\Administrator>curl localhost:3000
worker4,PID:2868
C:\Users\Administrator>curl localhost:3000
worker4,PID:2868
C:\Users\Administrator>curl localhost:3000
worker4,PID:2868
```

我们发现了cluster在win中的bug，只用到worker4。果断切换到Linux测试。

## 7. 用cluster实现负载均衡(Load Balance) -- ubuntu成功

###　Linux的系统环境

+ Linux: Ubuntu 12.04.2 64bit Server
+ Node: v0.11.2
+ Npm: 1.2.21

构建项目:不多解释

```{bash}
~ cd :/home/conan/nodejs/
~ mkdir nodejs-cluster && cd nodejs-cluster
~ vi server.js

var cluster = require('cluster');
var http = require('http');
var numCPUs = require('os').cpus().length;

if (cluster.isMaster) {
    console.log('[master] ' + "start master...");

    for (var i = 0; i < numCPUs; i++) {
         cluster.fork();
    }

    cluster.on('listening', function (worker, address) {
        console.log('[master] ' + 'listening: worker' + worker.id + ',pid:' + worker.process.pid + ', Address:' + address.address + ":" + address.port);
    });

} else if (cluster.isWorker) {
    console.log('[worker] ' + "start worker ..." + cluster.worker.id);
    http.createServer(function (req, res) {
        console.log('worker'+cluster.worker.id);
        res.end('worker'+cluster.worker.id+',PID:'+process.pid);
    }).listen(3000);
}
```

启动服务器

```{bash}

conan@conan-deskop:~/nodejs/nodejs-cluster$ node server.js
[master] start master...
[worker] start worker ...1
[master] listening: worker1,pid:2925, Address:0.0.0.0:3000
[worker] start worker ...3
[master] listening: worker3,pid:2931, Address:0.0.0.0:3000
[worker] start worker ...4
[master] listening: worker4,pid:2932, Address:0.0.0.0:3000
[worker] start worker ...2
[master] listening: worker2,pid:2930, Address:0.0.0.0:3000
worker4
worker2
worker1
worker3
worker4
worker2
worker1
```

用curl工具访问

```{bash}
C:\Users\Administrator>curl 192.168.1.20:3000
worker4,PID:2932
C:\Users\Administrator>curl 192.168.1.20:3000
worker2,PID:2930
C:\Users\Administrator>curl 192.168.1.20:3000
worker1,PID:2925
C:\Users\Administrator>curl 192.168.1.20:3000
worker3,PID:2931
C:\Users\Administrator>curl 192.168.1.20:3000
worker4,PID:2932
C:\Users\Administrator>curl 192.168.1.20:3000
worker2,PID:2930
C:\Users\Administrator>curl 192.168.1.20:3000
worker1,PID:2925
```

在Linux环境中，cluster是运行正确的！！！

## 8. cluster负载均衡策略的测试

我们在Linux下面，完成测试，用过测试软件： siege

安装siege

```{bash}
~ sudo apt-get install siege
```

启动node cluster

```{bash}
~ node server.js > server.log
```

运行siege启动命令,每秒50个并发请求。

```{bash}
~ sudo siege -c 50 http://localhost:3000

HTTP/1.1 200   0.00 secs:      16 bytes ==> /
HTTP/1.1 200   0.00 secs:      16 bytes ==> /
HTTP/1.1 200   0.00 secs:      16 bytes ==> /
HTTP/1.1 200   0.01 secs:      16 bytes ==> /
HTTP/1.1 200   0.00 secs:      16 bytes ==> /
HTTP/1.1 200   0.00 secs:      16 bytes ==> /
HTTP/1.1 200   0.00 secs:      16 bytes ==> /
HTTP/1.1 200   0.01 secs:      16 bytes ==> /
HTTP/1.1 200   0.00 secs:      16 bytes ==> /
HTTP/1.1 200   0.00 secs:      16 bytes ==> /
HTTP/1.1 200   0.00 secs:      16 bytes ==> /
HTTP/1.1 200   0.02 secs:      16 bytes ==> /
HTTP/1.1 200   0.00 secs:      16 bytes ==> /
HTTP/1.1 200   0.02 secs:      16 bytes ==> /
HTTP/1.1 200   0.01 secs:      16 bytes ==> /
HTTP/1.1 200   0.01 secs:      16 bytes ==> /
.....

^C
Lifting the server siege...      done.                                                                Transactions:                    3760 hits
Availability:                 100.00 %
Elapsed time:                  39.66 secs
Data transferred:               0.06 MB
Response time:                  0.01 secs
Transaction rate:              94.81 trans/sec
Throughput:                     0.00 MB/sec
Concurrency:                    1.24
Successful transactions:        3760
Failed transactions:               0
Longest transaction:            0.20
Shortest transaction:           0.00

FILE: /var/siege.log
You can disable this annoying message by editing
the .siegerc file in your home directory; change
the directive 'show-logfile' to false.
```

我们统计结果，执行3760次请求，消耗39.66秒，每秒处理94.81次请求。

查看server.log文件,

```{bash}

~  ls -l
total 64
-rw-rw-r-- 1 conan conan   756  9月 28 15:48 server.js
-rw-rw-r-- 1 conan conan 50313  9月 28 16:26 server.log

~ tail server.log
worker4
worker1
worker2
worker4
worker1
worker2
worker4
worker3
worker2
worker1
```

最后，用R语言分析一下:server.log

```{bash}
~ R 

> df<-read.table(file="server.log",skip=9,header=FALSE)
> summary(df)
       V1
 worker1:1559
 worker2:1579
 worker3:1570
 worker4:1535
```

我们看到，请求被分配到worker数据量相当。所以，cluster的负载均衡的策略，应该是随机分配的。

好了，我们又学了一个很有用的技能！利用cluster可以构建出多核应用，充分的利用多CPU带业的性能吧！！

#### 转载请注明出处：http://blog.fens.me/nodejs-core-cluster/

