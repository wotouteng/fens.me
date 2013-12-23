upstart封装nodejs应用为系统服务
==================

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/linux-upstart-nodejs/

![upstart封装nodejs应用为系统服务](http://blog.fens.me/wp-content/uploads/2013/06/upstart-nodejs.png)

#### 前言

本文将介绍封装nodejs应用为系统服务，应用会像一个守护程序一样，被操作系统所管理。

#### 目录：

1. nodejs应用介绍
2. upstart任务脚本
3. nodejs应用管理

## 1. nodejs应用介绍

终于要把nodejs的应用程序部署上线了, node和npm在ubuntu下安装，请参考：准备Nodejs开发环境Ubuntu

把源代码通过git复制到目录下面

```{bash}
/root/deploy/movie
```
然后搞命令:

```{bash}
~ cd /root/deploy/movie
node ./app.js
```

上面的方式，nodejs程序会在当前的console界面中运行，一旦console结束，应用也会停止。我们改一下命令，让程序在后台运行

```{bash}
~ node ./app.js &
[1] 21333
[2013-06-21 09:38:30.696] [INFO] console - Start App: http://moive.me
[2013-06-21 09:38:30.700] [INFO] console - Express server listening on port 3000
```

这样程序就就在后台启动了。进程正常运行着，我也不用做太多的事情。

如果我想停止这个程序，怎么办呢？ 找到nodejs的系统进程，再杀死。

```{bash}
~ ps -aux|grep node
root     21333  0.6  3.7 909200 38292 pts/0    Sl   09:38   0:00 node app.js
~ kill -9 21333  
```

直接暴力解决。如果能像系统服务一样，来启动和关闭nodejs应用，多好啊！下面就通过upstart来完成把nodejs应用封装为系统服务。

## 2. upstart任务脚本

upstart的使用在 upstart把应用封装成系统服务 一文中已经介绍过了。

```{bash}

~ vi /etc/init/nodejs-moive.conf

description "node.js moive.me"
author "bsspirit <http://blog.fens.me>"

start on startup
stop on shutdown

script
    export HOME="/root/deploy/movie"
    echo $$ > /var/run/moiveme.pid
    export NODE_ENV=production
    exec /usr/bin/node /root/deploy/movie/server.js

    #日志输出
    #exec /usr/bin/node /root/deploy/movie/server.js >> /var/log/moiveme.log 2>&1
end script

pre-start script
    echo "[`date -u +%Y-%m-%dT%T.%3NZ`] (sys) Starting" >> /var/log/moiveme.log
end script

pre-stop script
    rm /var/run/moiveme.pid
    echo "[`date -u +%Y-%m-%dT%T.%3NZ`] (sys) Stopping" >> /var/log/moiveme.log
end script
```

## 3. nodejs应用管理

启动nodejs-moive应用，进程ID：21257

```{bash}
~ start nodejs-moive
nodejs-moive start/running, process 21257

~ tail -f /var/log/moiveme.log
[2013-06-21T09:21:17.122Z] (moive.me) Starting

~ ps aux|grep node
root     21257  8.0  3.7 909204 37824 ?        Ssl  09:21   0:00 /usr/bin/node /root/deploy/movie/server.js
```

查看运行状态， 进程21257正常运行

```{bash}
~ status nodejs-moive
nodejs-moive start/running, process 21257
```

杀死nodejs应用进程21257，通过upstart管理，nodejs-moive应用会自动重启

```{bash}

~ kill -9 21257

#自动重启日志
~ tail -f /var/log/moiveme.log
[2013-06-21T09:21:33.662Z] (moive.me) Starting

#查看系统进程，发现进行ID变了
~ ps -aux|grep node
root     21280  9.1  3.7 909204 37704 ?        Ssl  09:21   0:00 /usr/bin/node /root/deploy/movie/server.js

#查看进程状态，进程ID确实变了，而且是自动完成的
~ status nodejs-moive
nodejs-moive start/running, process 21280
```

这样很方便地我们可以通过upstart，以系统服务的方式管理nodejs应用。运维起来会很容易！！

#### 转载请注明出处：http://blog.fens.me/linux-upstart-nodejs/
