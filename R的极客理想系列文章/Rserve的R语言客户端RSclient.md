Rserve的R语言客户端RSclient
==========

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-rserve-rsclient/

![Rserve的R语言客户端RSclient](http://blog.fens.me/wp-content/uploads/2013/11/rsclient-rserve.png)

#### 前言

RSclient是实现Rserve通信的R语言客户端程序，对于统计人员使用RSclient调用Rserve运行R语言脚本，感觉会很奇怪。但对于实际应用架构来说却是很有帮助的，不仅可以统一Rserve的接口，还可以从架构上实现R语言的跨虚拟机的分步式程序设计。

#### 目录

1. Rserve和RSclient介绍
2. Rserve系统环境
3. RSclient安装
4. RSclient的API
5. RSclient使用
6. 两个客户端同时访问

## 1. Rserve和RSclient介绍

### Rserve介绍

Rserve是一个基于TCP/IP协议的，允许R语言与其他语言通信的C/S结构的程序，支持C/C++,Java,PHP,Python,Ruby,Nodejs等。 Rserve提供远程连接，认证，文件传输等功能。我们可以设计R做为后台服务，处理统计建模，数据分析，绘图等的任务。

Rserve的使用请参考文章：[R语言服务器程序 Rserve详解](http://blog.fens.me/r-rserve-server/), [Rserve与Java的跨平台通信](http://blog.fens.me/r-rserve-java/)

### RSclient介绍

RSclient是Rserve客户端的R语言实现，通过RSclient可以访问Rserve服务器实例。

官方介绍：http://www.rforge.net/RSclient/

## 2. Rserve系统环境

### 服务器系统环境

* Linux: Ubuntu 12.04.2 LTS 64bit
* R: 3.0.1 x86_64-pc-linux-gnu
* Rserve: Rserve v1.7-1

启动Rserve服务器

```{bash}

~ R CMD Rserve

~ ps -aux|grep Rserve
conan    28339  0.0  1.2 116292 25240 ?        Ss   22:31   0:00 /usr/lib/R/bin/Rserve

~ netstat -ntlp|grep Rserve
tcp        0      0 0.0.0.0:6311            0.0.0.0:*               LISTEN      28339/Rserve
```

### Rserve环境

* IP: 192.168.1.201，允许远程访问
* 端口: 6311
* 登陆认证: 用户名:conan, 密码:conan
* 字符编码: utf-8

```{bash}
~ R CMD Rserve --RS-settings
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

## 3. RSclient安装

客户端环境，远程访问

* Win7 64bit
* R: 3.0.1 x86_64-w64-mingw32/x64 b4bit

RSclient安装和加载

```{bash}
~ R

> install.packages("RSclient")
> library(RSclient)
```

## 4. RSclient的API

RSclient的API分为两组，Rclient(旧版本)和RCC(新版本)。

Rclient 旧版本API：函数名是大小写连写的

```{bash}
RSassign           RSattach           RSclose
RSconnect          RSdetach           RSeval             RSevalDetach
RShowDoc           RSiteSearch        RSlogin            RSserverEval
RSserverSource     RSshutdown         RSclient::
```

RCC 新版本API：函数名以.分隔的

```{bash}
RS.assign          RS.authkey         RS.close           RS.collect
RS.connect         RS.eval            RS.eval.qap        RS.login
RS.oobCallbacks    RS.server.eval     RS.server.shutdown RS.server.source
RS.switch
```

我主要介绍新版本API的使用

客户端操作函数

* RS.connect: 创建与Rserve的连接
* RS.close: 关闭与Rserve的连接
* RS.login: 登陆认证
* RS.authkey: 设置认证时加密算法
* RS.eval: 在Rserve远程执行R语句
* RS.eval.qap: 远程执行R语句，使用Rserve QAP序列化对象代替本地对象
* RS.collect: 等待异步执行的eval的结果，一起返回
* RS.assign: 远程执行赋值
* RS.switch: 协议转换，从QAP1到TLS QAP1
* RS.oobCallbacks: 回调函数, 通过OOB_SEND函数和OOB_MSG函数中执行

服务器管理函数：Rserve启动时，需要设置–RS-enable-control

* RS.server.eval: 服务器控制函数，执行脚步
* RS.server.shutdown: 服务器控制函数，关闭服务器
* RS.server.source: 服务器控制函数，执行服务器端本地文件

## 5. RSclient的使用

```{bash}
~ R
> library(RSclient)

#建立连接
> conn<-RS.connect(host="192.168.1.201")
> conn
 Rserve QAP1 connection 0x000000000445cd60 (socket 308, queue length 0)

#登陆认证
> RS.login(conn,"conan","conan",authkey=RS.authkey(conn))
[1] TRUE

#执行脚本
> RS.eval(conn,rnorm(5))
[1] -2.6762608  1.4435144 -0.4298395 -0.7046573 -1.4056073

#设置变量
> RS.assign(conn,"xx",99)
raw(0)
> RS.eval(conn,xx-55)
[1] 44

#同步执行
> RS.eval(conn,head(rnorm(10000000)),wait=TRUE)
[1] -4.20217390  0.22353317 -1.70256992  0.30053213 -0.01427486 -0.70522254

#异步执行
> RS.eval(conn,head(rnorm(10000000)),wait=FALSE)
NULL
> RS.collect(conn)
[1] -0.2814752  0.3215521 -1.0978825 -0.8534461 -0.2459560 -0.4804882

#关闭连接
> RS.close(conn)
NULL
> conn
 Closed Rserve connection 0x000000000445cc80
```

## 6. 两个客户端同时访问

客户端A

```{bash}
~ R 
library(RSclient)
conn<-RS.connect(host="192.168.1.201")
RS.login(conn,"conan","conan",authkey=RS.authkey(conn))
RS.assign(conn,"A",1234)

> RS.eval(conn,A)
[1] 1234

> RS.eval(conn,getwd())
[1] "/tmp/Rserv/conn29039"
```

客户端B

```{bash}
~ R 
library(RSclient)
conn<-RS.connect(host="192.168.1.201")
RS.login(conn,"conan","conan",authkey=RS.authkey(conn))
RS.assign(conn,"B",5678)

> RS.eval(conn,B)
[1] 5678
> RS.eval(conn,ls())
[1] "B"
> RS.eval(conn,getwd())
[1] "/tmp/Rserv/conn29040"
```

我们看到A,B两个客户端建立接口后，服务器端的Rserve会在两个单独的空间运行。因此，A,B两个客户端的访问是独立的。

在客户端A，设置服务器全局变量

```{bash}
> RS.server.eval(conn, "G<-999") :
  command failed with status code 0x48: access denied
```

注：出现拒绝访问的错误，虽然我们已经打开了--RS-enable-control，还是有错误，不知道是不是Rserve的bug。

通过RSclient，我们就可以远程连接Rserve。如果大家发散一下想法，就可以通过Rserve和RSclient构建一个纯R的分步式计算环境。下篇文章我将模拟构建一个这样的环境！！

#### 转载请注明出处：http://blog.fens.me/r-rserve-rsclient/


