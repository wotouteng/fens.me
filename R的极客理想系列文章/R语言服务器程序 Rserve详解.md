R语言服务器程序 Rserve详解
===============

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-rserve-server/

![R语言服务器程序 Rserve详解](http://blog.fens.me/wp-content/uploads/2013/11/rserve-server.png)

#### 前言

Rserve作为一个R语言与其他语言的通信(TCP/IP)接口，被多个项目所依赖。Rserve服务端配置和运行都非常简单，客户端由多种语言实现，像C/C++, Java等。R也有自己的客户端实现 RSclient 项目，将在下文介绍。

本文详细剖析Rserve作为服务器端应用的配置和使用。

#### 目录

1. Rserve介绍
2. Rserve简单使用
3. Rserve高级使用：配置管理
4. Rserve高级使用：用户登陆认证

## 1. Rserve介绍

关于Rserve上次说的不够细，用到起来处处有坑。接下来，深入细节再学学。

上篇文章请参考：[Rserve与Java的跨平台通信](http://blog.fens.me/r-rserve-java/)

Rserve介绍

Rserve是一个基于TCP/IP协议的，允许R语言与其他语言通信的C/S结构的程序，支持C/C++,Java,PHP,Python,Ruby,Nodejs等。 Rserve提供远程连接，认证，文件传输等功能。我们可以设计R做为后台服务，处理统计建模，数据分析，绘图等的任务。

官方介绍：http://www.rforge.net/Rserve/

## 2. Rserve简单使用

### 服务器系统环境

* Linux: Ubuntu 12.04.2 LTS 64bit
* R: 3.0.1 x86_64-pc-linux-gnu

### Rserve安装和加载

```{bash}
~ R
> install.packages("Rserve")
> library(Rserve)
```

### Rserve服务器

* 1). 在程序中，启动Rserve服务器
* 2). 在命令行，启动Rserve服务器

Rserve的函数，只有3个。

* Rserve(): 单独启动一个守护进程作为Rserve实例。
* run.Rserve(): 在当前的进程中，启动Rserve实例
* self():

### 1). 在程序中，启动Rserve服务器

```{bash}
> library(Rserve)
> Rserve()
Starting Rserve:
 /usr/lib/R/bin/R CMD /home/conan/R/x86_64-pc-linux-gnu-library/3.0/Rserve/libs//Rserve


R version 3.0.1 (2013-05-16) -- "Good Sport"
Copyright (C) 2013 The R Foundation for Statistical Computing
Platform: x86_64-pc-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under certain conditions.
Type 'license()' or 'licence()' for distribution details.

  Natural language support but running in an English locale

R is a collaborative project with many contributors.
Type 'contributors()' for more information and
'citation()' on how to cite R or R packages in publications.

Type 'demo()' for some demos, 'help()' for on-line help, or
'help.start()' for an HTML browser interface to help.
Type 'q()' to quit R.

Rserv started in daemon mode.
```

查看Rserv进程

```{bash}
~ ps -aux|grep Rserve
conan     8830  0.0  1.2 116336 25044 ?        Ss   06:46   0:00 /home/conan/R/x86_64-pc-linux-gnu-library/3.0/Rserve/libs//Rserve

~ netstat -nltp|grep Rserve
tcp        0      0 127.0.0.1:6311          0.0.0.0:*               LISTEN      8830/Rserve
```

### 2). 在命令行，启动Rserve服务器

Rserve命令帮助

```{bash}
~ R CMD Rserve --help
Usage: R CMD Rserve []

Options: --help  this help screen
 --version  prints Rserve version (also passed to R)
 --RS-port   listen on the specified TCP port
 --RS-socket   use specified local (unix) socket instead of TCP/IP.
 --RS-workdir   use specified working directory root for connections.
 --RS-encoding   set default server string encoding to .
 --RS-conf   load additional config file.
 --RS-settings  dumps current settings of the Rserve
 --RS-source   source the specified file on startup.
 --RS-enable-control  enable control commands
 --RS-enable-remote  enable remote connections

All other options are passed to the R engine.
```

在命令行启动Rserve

```{bash}
~ R CMD Rserve --RS-enable-remote

R version 3.0.1 (2013-05-16) -- "Good Sport"
Copyright (C) 2013 The R Foundation for Statistical Computing
Platform: x86_64-pc-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under certain conditions.
Type 'license()' or 'licence()' for distribution details.

  Natural language support but running in an English locale

R is a collaborative project with many contributors.
Type 'contributors()' for more information and
'citation()' on how to cite R or R packages in publications.

Type 'demo()' for some demos, 'help()' for on-line help, or
'help.start()' for an HTML browser interface to help.
Type 'q()' to quit R.

Rserv started in daemon mode.
```

查看Rserv进程

```{bash}
~ ps -aux|grep Rserve
conan    27639  0.0  1.2 116288 25236 ?        Ss   20:41   0:00 /usr/lib/R/bin/Rserve --RS-enable-remote

~ netstat -nltp|grep Rserve
tcp        0      0 0.0.0.0:6311            0.0.0.0:*               LISTEN      27639/Rserve
```

## 3. Rserve高级使用：Rserve配置管理

通过配置文件，管理Rserve服务器。

* Rserv.conf: 配置文件
* source.R:初始化脚步文件

查看当前的Rserve 服务器的默认配置信息

```{bash}
~ R CMD Rserve --RS-settings
Rserve v1.7-1

config file: /etc/Rserv.conf
working root: /tmp/Rserv
port: 6311
local socket: [none, TCP/IP used]
authorization required: no
plain text password: not allowed
passwords file: [none]
allow I/O: yes
allow remote access: no
control commands: no
interactive: yes
max.input buffer size: 262144 kB
```

* config file: 我本地无此文件/etc/Rserv.conf, 系统会默认跳过这项
* working root: R运行时工作目录 /tmp/Rserv
* port: 通信端口6311
* local socket: TCP/IP协议
* authorization: 认证未开启
* plain text password: 不允许明文密码
* passwords file: 密码文件，未指定
* allow I/O: 允许IO操作
* allow remote access: 远程访问未开启
* control commands: 命令控制未开启
* interactive: 允许通信
* max.input buffer size: 文件上传限制262mb

修改默认配置，新建文件：/etc/Rserv.conf

```{bash}
~ sudo vi /etc/Rserv.conf

workdir /tmp/Rserv
remote enable
fileio enable
interactive yes
port 6311
maxinbuf 262144
encoding utf8
control enable
source /home/conan/R/RServe/source.R
eval xx=1

~ vi /home/conan/R/RServe/source.R

cat("This is my Rserve!!")
print(paste("Server start at",Sys.time()))
```

再次查看服务器配置

```{bash}
 R CMD Rserve --RS-settings
Rserve v1.7-1

config file: /etc/Rserv.conf
working root: /tmp/Rserv
port: 6311
local socket: [none, TCP/IP used]
authorization required: yes
plain text password: allowed
passwords file: [none]
allow I/O: yes
allow remote access: yes
control commands: yes
interactive: yes
max.input buffer size: 262144 kB
```

* 允许远程访问
* 需要登陆认证
* 允许明文密码

启动服务器

```{bash}
~  R CMD Rserve

R version 3.0.1 (2013-05-16) -- "Good Sport"
Copyright (C) 2013 The R Foundation for Statistical Computing
Platform: x86_64-pc-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under certain conditions.
Type 'license()' or 'licence()' for distribution details.

  Natural language support but running in an English locale

R is a collaborative project with many contributors.
Type 'contributors()' for more information and
'citation()' on how to cite R or R packages in publications.

Type 'demo()' for some demos, 'help()' for on-line help, or
'help.start()' for an HTML browser interface to help.
Type 'q()' to quit R.

This is my Rserve!![1] "Server start at 2013-10-30 22:38:10"
Rserv started in daemon mode.
```

查看日志：source.R在启动时候被执行。

```{bash}
"This is my Rserve!![1] "Server start at 2013-10-30 22:38:10""
```

source选项，用来配置Rserve服务器启动时加载的文件，例如：初始化系统变量，初始化系统函数等。
eval选项，直接定义环境变量。


查看进程

```{bash}
~ ps -aux|grep Rserve
conan    28339  0.0  1.2 116292 25240 ?        Ss   22:31   0:00 /usr/lib/R/bin/Rserve

~ netstat -ntlp|grep Rserve
tcp        0      0 0.0.0.0:6311            0.0.0.0:*               LISTEN      28339/Rserve
```

允许远程访问, 不限制IP

```{bash}
0 0.0.0.0:6311
```

## 4. Rserve高级使用：用户登陆认证

在本地环境，无认证情况下，使用RSclient访问Rserve。RSclient的使用，请参考文章：[Rserve的R语言客户端RSclient](http://blog.fens.me/r-rserve-rsclient/)

```{bash}
~ R

>  library(RSclient)
>  conn<-RS.connect()
>  RS.eval(conn,rnorm(10))
 [1]  0.03230305  0.95710725 -0.33416069 -0.37440009 -1.95515719 -0.22895924
 [7]  0.39591984  1.67898842 -0.01666688 -0.26877775
```

增加用户登陆认证，修改文件：/etc/Rserv.conf

```{bash}
~ sudo vi /etc/Rserv.conf

workdir /tmp/Rserv
remote enable
fileio enable
interactive yes
port 6311
maxinbuf 262144
encoding utf8
control enable
source /home/conan/R/RServe/source.R
eval xx=1
auth required
plaintext enable
```

使用RSclient访问，认证报错。

```{bash}
> library(RSclient)
> conn<-RS.connect()
> RS.eval(conn,rnorm(10))
Error in RS.eval(conn, rnorm(10)) :
  command failed with status code 0x41: authentication failed
```

用RSclient登陆

```{bash}
> library(RSclient)
> conn<-RS.connect()
> RS.login(conn,"conan","conan",authkey=RS.authkey(conn))
[1] TRUE
> RS.eval(conn,rnorm(5))
[1] -1.19827684  0.72164617  0.22225934  0.09901505 -1.54661436
```

这里用户登陆认证，是绑定的操作系统用户。我们还可以在Rserv.conf配置文件，指定uid,gid参数，从而更细粒度的控制服务器权限。

本文详细地介绍了Rserve的安装，配置，使用，以及Rserve支持的功能。有了知识积累，我们就可以利用Rserve，来构建企业级的上线应用了。

#### 转载请注明出处：http://blog.fens.me/r-rserve-server/

