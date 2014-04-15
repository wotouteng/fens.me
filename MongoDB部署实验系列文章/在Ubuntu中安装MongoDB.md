在Ubuntu中安装MongoDB
============

[MongoDB部署实验系列文章](http://blog.fens.me/series-mongodb/)，MongoDB做为NoSQL数据库，最近几年持续升温，越来越多的企业都开始尝试用MongoDB代替原有Database做一些事情。MongoDB也在集群，分片，复制上也有相当不错的的表现。我通过将做各种MongoDB的部署实验进行介绍。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/linux-mongodb-install/

![在Ubuntu中安装MongoDB](http://blog.fens.me/wp-content/uploads/2014/03/linux-mongo-install.png)

#### 前言

MongoDB作为一种文档型的NoSQL数据库，使用起来非常灵活，回避了关系型数据库前期的复杂数据库设计。MongoDB存储基于JSON格式，同时用Javascript做为数据库操作语言，给了使用者无限想象的空间，可以通过编程在MongoDB服务器中解决非常复杂的条件查询的问题。

#### 目录

1. MongoDB在Windows中安装
2. MongoDB在Linux Ubuntu中安装
3. 通过命令行客户端访问MongoDB

## 1. MongoDB在Windows中安装

在Windows系统上安装MongoDB数据库是件非常简单的事情，下载可执行安装文件(exe)，双击安装即可。下载地址：http://www.mongodb.org/downloads

* MongoDB服务器运行命令：MongoDB安装目录/bin/mongod.exe
* MongoDB客户端运行命令：MongoDB安装目录/bin/mongo.exe

## 2. MongoDB在Linux Ubuntu中安装

本文使用的Linux是Ubuntu 12.04.2 LTS 64bit的系统，安装MongoDB数据库软件包可以通过apt-get实现。但我们修要安装官方提供MongoDB软件源。

修改apt的source.list文件，增加10gen的设置。

```{bash}

# 下载密钥文件
~  sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10
Executing: gpg --ignore-time-conflict --no-options --no-default-keyring --secret-keyring /tmp/tmp.kVFab9XYw0 --trustdb-name /etc/apt/trustdb.gpg --keyring /etc/apt/trusted.gpg --primary-keyring /etc/apt/trusted.gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10
gpg: 下载密钥‘7F0CEB10’，从 hkp 服务器 keyserver.ubuntu.com
gpg: 密钥 7F0CEB10：公钥“Richard Kreuter ”已导入
gpg: 没有找到任何绝对信任的密钥
gpg: 合计被处理的数量：1
gpg:               已导入：1  (RSA: 1)

# 在source.list中增加MongoDB源的配置
~ echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' | sudo tee /etc/apt/sources.list.d/mongodb.list
deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen

# 更新软件源
~ sudo apt-get update
```

在Linux Ubuntu中安装MongoDB数据库

```{bash}
#安装MongoDB服务器端
~ sudo apt-get install mongodb-10gen
```

安装完成后，MongoDB服务器会自动启动，我们检查MongoDB服务器程序

```{bash}

# 检查MongoDB服务器系统进程
~  ps -aux|grep mongo
mongodb   6870  3.7  0.4 349208 39740 ?        Ssl  10:27   2:23 /usr/bin/mongod --config /etc/mongodb.conf

# 通过启动命令检查MongoDB服务器状态
~  netstat -nlt|grep 27017
tcp        0      0 0.0.0.0:27017           0.0.0.0:*               LISTEN

# 通过启动命令检查MongoDB服务器状态
~ sudo /etc/init.d/mongodb status
Rather than invoking init scripts through /etc/init.d, use the service(8)
utility, e.g. service mongodb status

Since the script you are attempting to invoke has been converted to an
Upstart job, you may also use the status(8) utility, e.g. status mongodb
mongodb start/running, process 6870

# 通过系统服务检查MongoDB服务器状态
~ sudo service mongodb status
mongodb start/running, process 6870
```

通过web的控制台，查看MongoDB服务器的状态。在浏览器输入 http://ip:28017 ，就可以打开通过web的控制台了。

![](http://blog.fens.me/wp-content/uploads/2014/03/mongodb-web.png)

## 3. 通过命令行客户端访问MongoDB

安装MongoDB服务器，会自动地一起安装MongoDB命令行客户端程序。

在本机输入mongo命令就可以启动，客户端程序访问MongoDB服务器。

```{bash}

~ mongo
MongoDB shell version: 2.4.9
connecting to: test
Welcome to the MongoDB shell.
For interactive help, type "help".
For more comprehensive documentation, see

http://docs.mongodb.org/

Questions? Try the support group

http://groups.google.com/group/mongodb-user

# 查看命令行帮助
> help
        db.help()                    help on db methods
        db.mycoll.help()             help on collection methods
        sh.help()                    sharding helpers
        rs.help()                    replica set helpers
        help admin                   administrative help
        help connect                 connecting to a db help
        help keys                    key shortcuts
        help misc                    misc things to know
        help mr                      mapreduce

        show dbs                     show database names
        show collections             show collections in current database
        show users                   show users in current database
        show profile                 show most recent system.profile entries with time >= 1ms
        show logs                    show the accessible logger names
        show log [name]              prints out the last segment of log in memory, 'global' is default
        use                 set current database
        db.foo.find()                list objects in collection foo
        db.foo.find( { a : 1 } )     list objects in foo where a == 1
        it                           result of the last line evaluated; use to further iterate
        DBQuery.shellBatchSize = x   set default number of items to display on shell
        exit                         quit the mongo shell

```

MongoDB服务器，默认情况下是允许外部访问的。这样单节的MongoDB，我们已经成功地安装在Linux Ubuntu系统中。

#### 转载请注明出处：http://blog.fens.me/linux-mongodb-install/






