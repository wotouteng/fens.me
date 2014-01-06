FastRWeb跑在Web上的R程序
===========

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-fastrweb-rserve/

![FastRWeb跑在Web上的R程序](http://blog.fens.me/wp-content/uploads/2013/11/FastRWeb.png)

#### 前言

一直以来R语言都被用在，基于个人电脑的客户端程序上。我们习惯性的下载R安装包，在自己的电脑上面装好，写算法，运行。然后，以贴图或者文档的形式，发表自己的作品。

R如果可以运行在Server端，把结果以Web的方式发布！这才是互联网的工作方式！FastRWeb提供一种方式，可以实现B/S结构的R应用。

#### 目录

1. FastRWeb介绍
2. FastRWeb架构原理
3. FastRWeb安装
4. FastRWeb使用

## 1. FastRWeb介绍

FastRWeb 是一个基础架构环境, 可以让R脚本运行在任何WebServer上，展示数据和图形。用户通过URL地址与R脚本进行通信交互。 FastRWeb 可以快速地构建一个R的Web环境，FastRWeb 底层依赖于CGI程序，也就是说，只要能支持CGI程序的WebServer，就可以运行FastRWeb。

本文以R语言的WebServer库Rserve为例，部署FastRWeb，实现R的Web应用。

官方发布页：http://www.rforge.net/FastRWeb/

## 2. FastRWeb架构原理

![](http://blog.fens.me/wp-content/uploads/2013/11/fastRweb-architect.png)

* 1. 浏览器通过http请求访问Web服务器
* 2. Web服务器通过socket，把请求发给Rserve服务器
* 3. Rserve调用FastRWeb环境，运行R的脚本，返回数据和图片
* 4. 浏览器，得到结果，并在web上展示

## 3. FastRWeb安装

### 服务器系统环境

* Linux: Ubuntu 12.04.2 LTS 64bit
* R: 3.0.1 x86_64-pc-linux-gnu

### FastRWeb安装

```{bash}
~ R
> install.packages("FastRWeb")
installing to /home/conan/R/x86_64-pc-linux-gnu-library/3.0/FastRWeb/libs
** R
** inst
** preparing package for lazy loading
** help
*** installing help indices
** building package indices
** testing if installed package can be loaded
* DONE (FastRWeb)
```

由于FastRWeb依赖于cairo库，Cairo安装会用到Linux本地库，请参考文章：[R语言的高质量图形渲染库Cairo](http://blog.fens.me/r-cairo/)

### 安装Rserve

```{bash}
> install.packages("Rserve")
```

有关Rserve的介绍，请参考文章：[Rserve与Java的跨平台通信](http://blog.fens.me/r-rserve-java/)

### 创建FastRWeb基于Rserve的运行环境

```{bash}

~ cd /home/conan/R/x86_64-pc-linux-gnu-library/3.0/FastRWeb

~ sudo ./install.sh
Done.
Please check files in /var/FastRWeb/code
If they match tour setup, you can start Rserve using
/var/FastRWeb/code/start
```

### 查看生成的目录：/var/FastRWeb/code/

```{bash}
~ cd /var/FastRWeb/code/

~ ls -l
-rw-r--r-- 1 conan conan 1210 Oct 29 16:07 README
-rw-r--r-- 1 conan conan   79 Oct 29 17:50 rserve.conf
-rw-r--r-- 1 conan conan 2169 Oct 29 17:51 rserve.R
-rwxr-xr-x 1 conan conan  457 Oct 29 17:35 start
```

* README:帮助说明
* rserve.conf:Rserve的启动参数
* rserve.R:Rserve的启动脚本
* start:启动命令

### 修改文件：rserve.conf

```{bash}
~ vi rserve.conf

http.port 8888
remote enable
source /var/FastRWeb/code/rserve.R
control enable
```

默认情况，rserve是提供socket通信接口，为了web测试方便，我们改成用http的通信接口。

### 修改文件：rserve.R, 在文件上的最上面增加2行代码。

```{bash}
~ vi rserve.R

library(FastRWeb)
.http.request <- FastRWeb:::.http.request

## 原文代码
## ...
```

这样我们就改好了，成http为通信接口。

## 4. FastRWeb使用

### 启动服务

```{bash}
~ sudo ./start

R CMD Rserve --RS-conf /var/FastRWeb/code/rserve.conf --vanilla --no-save --RS-enable-remote

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

Starting Rserve on conan
Loading packages...
XML: TRUE
Cairo: TRUE
Matrix: TRUE
FastRWeb: TRUE
Rserv started in daemon mode.
```

从启动日志，我们看出XML, Cairo, Matrix, FastRWeb, Rserv几个包都加载了，正常运行。

### 查看系统进程和端口：

```{bash}
~ ps -aux|grep Rserve
conan    23739  0.0  1.4 120140 28916 ?        Ss   16:47   0:00 /usr/lib/R/bin/Rserve --RS-conf /var/FastRWeb/code/rserve.conf --vanilla --no-save --RS-enable-remote

~ sudo netstat -nltp|grep Rserve
tcp        0      0 0.0.0.0:8888            0.0.0.0:*               LISTEN      25778/Rserve
tcp        0      0 0.0.0.0:6311            0.0.0.0:*               LISTEN      25778/Rserve
```

有2个端口被打开了，一个是Rseve的socket端口6311，一个是http端口8888。

通过web进行访问：http://192.168.1.201:8888/example1.png

![](http://blog.fens.me/wp-content/uploads/2013/11/fastRweb-example1.png)

### 这个图的R代码，实际对应的是文件：/var/FastRWeb/web.R/example1.png.R

```{bash}
~ vi /var/FastRWeb/web.R/example1.png.R

run <- function(...) {
  p <- WebPlot(600, 600)
  plot(rnorm(100), rnorm(100), pch = 19, col = 2)
  p
}
```

### 修改：/var/FastRWeb/web.R/example1.png.R

```{bash}
~ vi /var/FastRWeb/web.R/example1.png.R

run <- function(...) {
  p <- WebPlot(600, 600)
  plot(rnorm(400), rnorm(400), pch = 20, col = 3)
  p
}
```

### 在浏览器刷新页面：

![](http://blog.fens.me/wp-content/uploads/2013/11/fastRweb-example2.png)

这样就实现了，通过web来执行R脚本，在web上画图。

### 在目录：/var/FastRWeb/web.R/，下面有几个例子，大家可以参考写自己的R语言脚本。

```{bash}
ls -l /var/FastRWeb/web.R
total 32
-rw-r--r-- 1 conan conan 790 Oct 29 16:07 common.R
-rw-r--r-- 1 conan conan 316 Oct 29 20:01 example1.png.R
-rw-r--r-- 1 conan conan 520 Oct 29 16:07 example2.R
-rw-r--r-- 1 conan conan 174 Oct 29 16:07 index.R
-rw-r--r-- 1 conan conan 215 Oct 29 16:07 info.R
-rw-r--r-- 1 conan conan  64 Oct 29 16:07 main.R
-rw-r--r-- 1 conan conan 167 Oct 29 16:07 README
-rw-r--r-- 1 conan conan 214 Oct 29 16:07 tmp.R
```

example2.R，可以接受页面传参数。

根据FastRWeb的说明，FastRWeb可以通过CGI，与任何的WebServer通信，这样我们就可以把R脚本部署在PHP, Python, Ruby, Java等高级语言的服务器上面了。

R-bloggers上有一篇文章，讲FastRWeb部署在基于Apache的XAMPP环境中:

http://www.r-bloggers.com/setting-up-fastrweb-on-mac-os-x/。

感兴趣的同学，自行尝试吧！！

#### 转载请注明出处：http://blog.fens.me/r-fastrweb-rserve/


