upstart封装mongodb应用为系统服务
===============

[ubuntu实用工具系列文章](http://blog.fens.me/series-ubuntu/)，将介绍基于Linux ubuntu的各种工具软件的配置和使用。有些工具大家早已耳熟能详，有些工具经常用到但确依然陌生。我将记录我在使用操作系统时，安装及配置工具上面的一些方法，把使用心得记录下来也便于自己的以后查找和回忆。

#### 关于作者
+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/linux-upstart-mongodb/

![upstart封装mongodb应用为系统服务](http://blog.fens.me/wp-content/uploads/2013/06/upstart-mongodb.png)

#### 前言

本文将介绍封装mongodb应用为系统服务，mongod应用会像一个守护程序一样，被操作系统所管理。通过upstart以系统服务的方式管理mongodb应用。运维起来也会很容易！！

本次实验是针对单个进程mongodb的，如果是mongodb集群，效果会更加明显的。特别进程被非法停止后的自动重启功能，增加了系统的健壮性。

#### 文章目录：

1. mongodb环境介绍
2. upstart任务脚本
3. mongodb应用管理

## 1. Mongodb环境介绍

moive.me是一个nodejs应用，使用mongodb作为数据存储。nodejs开发请参考：[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)

正常情况mongodb的启动命令

```{bash}
~ /usr/bin/mongod --config /etc/mongodb-moive.conf
```

为mongodb配置启动参数，配置文件mongodb-moive.conf

```{bash}
~ vi /etc/mongodb-moive.conf

dbpath=/var/lib/mongodb
logpath=/var/log/mongodb/mongodb-moive.log
logappend=true
bind_ip = 127.0.0.1
port = 27017
journal=true
#fork=true     #如果打开fork, 则upstart的stop,status命令失效。
```

上面的方式，应用程序会在当前的console界面中运行，一旦console结束，应用也会停止。我们改一下命令，让程序在后台运行。

```{bash}
~ /usr/bin/mongod --config /etc/mongodb-moive.conf &
```

这样程序就就在后台启动了。进程正常运行着，我也不用做太多的事情。

如果我想停止这个程序，怎么办呢？ 找到mongod的系统进程,再杀死。如果我们系统中跑着多个mongod的进程，那么找起来也是一个工作量，而且如果杀错了进程，后果不堪设想。多进程的mongod请参考[MongoDB部署实验系列文章](http://blog.fens.me/series-mongodb/)

如果moive的单个应用，能像系统服务一样，通过start, stop, status管理，那将会是多么方便的一件事啊！

## 2. upstart任务脚本

upstart的使用在[upstart把应用封装成系统服务](http://blog.fens.me/linux-upstart/)一文中已经介绍过了。

```{bash}
~ vi /etc/init/mongodb-moive.conf

description "mongodb moive.me"
author "bsspirit <http://blog.fens.me>"

limit nofile 20000 20000

kill timeout 300

respawn
respawn limit 2 5

pre-start script
    mkdir -p /var/lib/mongodb/
    mkdir -p /var/log/mongodb/
end script

start on runlevel [2345]
stop on runlevel [06]

script
    exec  /usr/bin/mongod --config /etc/mongodb-moive.conf
end script
```

## 3. Mongodb应用管理

启动mongodb-moive应用，进程ID：2037

```{bash}
~ start mongodb-moive
mongodb-moive start/running, process 2037

~ ps -aux|grep mongo
root      2037  0.7  1.5 705112 15960 ?        Ssl  07:53   0:00 /usr/bin/mongod --config /etc/mongodb-moive.conf
```

查看运行状态， 进程2037正常运行

```{bash}
~ status mongodb-moive
mongodb-moive start/running, process 2037

~ mongo
MongoDB shell version: 2.0.4
connecting to: test

> show dbs
local   (empty)
nodejs  0.203125GB
session 0.203125GB
```

非法关闭测试：杀死nodejs应用进程2037，通过upstart管理，mongodb-moive应用会自动重启

```{bash}
~ kill -9 2037

#查看系统进程，发现进程ID变了  
~ ps -aux|grep mongo
root      2054  2.0  1.5 638548 15872 ?        Ssl  07:53   0:00 /usr/bin/mongod --config /etc/mongodb-moive.conf

#查看进程状态，进程ID确实变了，而且是自动完成的
~ status mongodb-moive
mongodb-moive start/running, process 2054

#命令进行mongo
~ mongo
MongoDB shell version: 2.0.4
connecting to: test

> show dbs
local   (empty)
nodejs  0.203125GB
session 0.203125GB
```

刚才mongod被进程杀死时的日志, 2037被杀死，2054自动重启。		
```{bash}

***** SERVER RESTARTED *****

Sat Jun 22 07:53:35 [initandlisten] MongoDB starting : pid=2037 port=27017 dbpath=/var/lib/mongodb 64-bit host=li478-194
Sat Jun 22 07:53:35 [initandlisten] db version v2.0.4, pdfile version 4.5
Sat Jun 22 07:53:35 [initandlisten] git version: nogitversion
Sat Jun 22 07:53:35 [initandlisten] build info: Linux lamiak 2.6.42-37-generic #58-Ubuntu SMP Thu Jan 24 15:28:10 UTC 2013 x86_64 BOOST_LIB_VERSION=1_46_1
Sat Jun 22 07:53:35 [initandlisten] options: { config: "/etc/mongodb-moive.conf", dbpath: "/var/lib/mongodb", journal: "true", logappend: "true", logpath: "/var/log/mongodb/mongodb-moive.log" }
Sat Jun 22 07:53:35 [initandlisten] journal dir=/var/lib/mongodb/journal
Sat Jun 22 07:53:35 [initandlisten] recover : no journal files present, no recovery needed
Sat Jun 22 07:53:35 [initandlisten] waiting for connections on port 27017
Sat Jun 22 07:53:35 [websvr] admin web console waiting for connections on port 28017
Sat Jun 22 07:53:38 [initandlisten] connection accepted from 127.0.0.1:37554 #1
Sat Jun 22 07:53:39 [conn1] end connection 127.0.0.1:37554

***** SERVER RESTARTED *****

Sat Jun 22 07:53:56 [initandlisten] MongoDB starting : pid=2054 port=27017 dbpath=/var/lib/mongodb 64-bit host=li478-194
Sat Jun 22 07:53:56 [initandlisten] db version v2.0.4, pdfile version 4.5
Sat Jun 22 07:53:56 [initandlisten] git version: nogitversion
Sat Jun 22 07:53:56 [initandlisten] build info: Linux lamiak 2.6.42-37-generic #58-Ubuntu SMP Thu Jan 24 15:28:10 UTC 2013 x86_64 BOOST_LIB_VERSION=1_46_1
Sat Jun 22 07:53:56 [initandlisten] options: { config: "/etc/mongodb-moive.conf", dbpath: "/var/lib/mongodb", journal: "true", logappend: "true", logpath: "/var/log/mongodb/mongodb-moive.log" }
Sat Jun 22 07:53:56 [initandlisten] journal dir=/var/lib/mongodb/journal
Sat Jun 22 07:53:56 [initandlisten] recover begin
Sat Jun 22 07:53:56 [initandlisten] info no lsn file in journal/ directory
Sat Jun 22 07:53:56 [initandlisten] recover lsn: 0
Sat Jun 22 07:53:56 [initandlisten] recover /var/lib/mongodb/journal/j._0
Sat Jun 22 07:53:56 [initandlisten] recover cleaning up
Sat Jun 22 07:53:56 [initandlisten] removeJournalFiles
Sat Jun 22 07:53:56 [initandlisten] recover done
Sat Jun 22 07:53:56 [websvr] admin web console waiting for connections on port 28017
Sat Jun 22 07:53:56 [initandlisten] waiting for connections on port 27017
Sat Jun 22 07:54:04 [initandlisten] connection accepted from 127.0.0.1:37559 #1
Sat Jun 22 07:54:56 [clientcursormon] mem (MB) res:47 virt:1008 mapped:160
Sat Jun 22 07:59:56 [clientcursormon] mem (MB) res:47 virt:1008 mapped:160
Sat Jun 22 08:00:25 [conn1] end connection 127.0.0.1:37559
```

正常关闭mongodb测试：通过stop命令

```{bash}
~ stop mongodb-moive
mongodb-moive stop/waiting

~ status mongodb-moive
mongodb-moive stop/waiting

~  ps -aux|grep mongo
```

正常关闭mongodb测试：通过mongo命令

```{bash}

~ mongo
MongoDB shell version: 2.0.4
connecting to: test

> use admin
switched to db admin

> db.shutdownServer()
Sat Jun 22 08:10:11 DBClientCursor::init call() failed
Sat Jun 22 08:10:11 query failed : admin.$cmd { shutdown: 1.0 } to: 127.0.0.1
server should be down...
Sat Jun 22 08:10:11 trying reconnect to 127.0.0.1
Sat Jun 22 08:10:11 reconnect 127.0.0.1 failed couldn't connect to server 127.0.0.1
Sat Jun 22 08:10:11 Error: error doing query: unknown shell/collection.js:151
>
bye

~ ps -aux|grep mongo
root      2332  0.6  1.5 705112 15960 ?        Ssl  08:10   0:00 /usr/bin/mongod --config /etc/mongodb-moive.conf
```

我们看们通过mongo的shutdownServer()命令，mongo也会重启，可能是runlevel的设置问题。我们选择要不要使用respawn的重启功能。

我们已经按照moive应用的需求，配置好了mongodb-moive启动程序。

系统运维也将变得如此简单。

#### 转载请注明出处：http://blog.fens.me/linux-upstart-mongodb/

