在巨人的肩膀前行 催化R包开发
==========

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-package-faster/

![在巨人的肩膀前行 催化R包开发](http://blog.fens.me/wp-content/uploads/2013/08/r-devtools.png)

#### 前言

在 [开发自己的R包sayHello](http://blog.fens.me/r-build-package/) 一文中，我们看到了如何从底层，按照R语言的标准构建一个R语言的扩展包。但实施过程确实复杂，会让很多的统计学工作者望而却步。如果能有一种比较简单方式，简化开发过程，那该多好啊！

我们是幸运的，Hadley Wickham(ggplot2,plyr的作者)已经帮我们梳理出了一套开发流程。站在巨人的肩膀上，看得更高，走得更远。

#### 目录

1. 开发流程介绍
2. 编写功能代码
3. 调试程序
4. 单元测试
5. 撰写文档
6. 程序打包
7. 程序发布

## 1. 开发流程介绍

站在巨人的肩膀，开发R包我们有3个武器：devtools, roxygen2, testthat

* devtools：让开发变得简单，各种开发小工具的合集，非常实用。
* roxygen2：通过注释的方式，生成文档，远离Latex的烦恼。
* testthat：单元测试，让R包稳定、健壮，减少升级的痛苦。

### 标准化的开发流程：

1. 编写功能代码
2. 调试程序
3. 单元测试
4. 撰写文档
5. 程序打包

## 2. 编写功能代码

### 1). 安装程序包：devtools, roxygen2, testthat

```{base}
#依赖库
~ sudo apt-get install libcurl4-openssl-dev

#请使用root权限启动R
~ sudo R

> install.packages("devtools")
> install.packages("roxygen2")
> install.packages("testthat")

> library(devtools)
> library(roxygen2)
> library(testthat)

#查看
> search()
 [1] ".GlobalEnv"        "package:testthat"  "package:roxygen2"
 [4] "package:digest"    "package:stats"     "package:graphics"
 [7] "package:grDevices" "package:utils"     "package:datasets"
[10] "package:methods"   "Autoloads"         "package:base"
```

### 构建工程chinaWeather

创建骨架,代替package.skeleton()

```{base}
> setwd("/home/conan/R")

> create("/home/conan/R/chinaWeather")
Creating package chinaWeather in /home/conan/R
No DESCRIPTION found. Creating default:

Package: chinaWeather
Title:
Description:
Version: 0.1
Authors@R: # getOptions('devtools.desc.author')
Depends: R (>= 3.0.1)
License: # getOptions('devtools.desc.license')
LazyData: true

> setwd("/home/conan/R/chinaWeather")
> dir()
[1] "DESCRIPTION" "man"         "R"
```

### 编辑DESCRIPTION文件

```{base}
~ vi /home/conan/R/chinaWeather/DESCRIPTION
Package: chinaWeather
Type: Package
Title: a visualized package for china Weather
Description: a visualized package for china Weather
Author: Dan Zhang
Maintainer: Dan Zhang
Version: 0.1
Depends: R (>= 3.0.1)
License: GPL-2
LazyData: true
Date: 2013-08-05
```

### 增加函数文件chinaWeather.R

```{base}
~ vi /home/conan/R/chinaWeather/R/chinaWeather.R

#define a filename from current date
filename<-function(date=Sys.time()){
  paste(format(date, "%Y%m%d"),".csv",sep="")
}
```

## 3. 调试程序

加载程序包到R中

```{base}
> load_all("/home/conan/R/chinaWeather")
Loading chinaWeather

> filename
function(date=Sys.time()){
  paste(format(date, "%Y%m%d"),".csv",sep="")
}


> filename()
[1] "20130805.csv"

> day<-as.Date("20110701",format="%Y%m%d")
> filename(day)
[1] "20110701.csv"
```

## 4. 单元测试

### 编写单元测试代码

```{base}
~ mkdir /home/conan/R/chinaWeather/inst/tests
~ vi /home/conan/R/chinaWeather/inst/tests/test.chinaWeather.R

library(testthat)
context("filename: current of date")

test_that("filename is current of date", {
  daystr<-paste(format(Sys.Date(), "%Y%m%d"),".csv",sep="")
  expect_that(filename(), equals(daystr))

  day<-as.Date("20110701",format="%Y%m%d")
  expect_that(filename(day), equals("20110701.csv"))
})
```

### 运行单元测试

```{base}
#单个文件的单元测试
> source("/home/conan/R/chinaWeather/inst/tests/test.chinaWeather.R")
> test_file("/home/conan/R/chinaWeather/inst/tests/test.chinaWeather.R")
filename: current of date : ..

#对目录下所有文件的单元测试
> test_dir("/home/conan/R/chinaWeather/inst/tests/",reporter = "summary")
filename: current of date : ..

#自动单元测试
> src<-"/home/conan/R/chinaWeather/R/"
> test<-"/home/conan/R/chinaWeather/inst/tests/"
> auto_test(src,test)
filename: current of date : ..

#对package执行测试
> test("/home/conan/R/chinaWeather")
Testing chinaWeather
Loading chinaWeather
filename: current of date : ..
```

完成单元测试！下面开始撰写文档。

## 5. 撰写文档

这里撰写文档，使用源代码注释的方式，然后生成latex，再生成doc。比起原始的直接写latex要容易的多。

### 打开源代码文件：chinaWeather.R

```{base}

~ vi /home/conan/R/chinaWeather/R/chinaWeather.R

#' Define a filename from current date.
#'
#' @param date input a date type
#' @return character a file name
#' @keywords filename 
#' @export
#' @examples
#' filename()
#' filename(as.Date("20110701",format="%Y%m%d"))
filename<-function(date=Sys.time()){
  paste(format(date, "%Y%m%d"),".csv",sep="")
}
```

### 生成latex文档

```{base}
> library(roxygen2)
Loading required package: digest

> roxygenize("/home/conan/R/chinaWeather")
Updating collate directive in  /home/conan/R/chinaWeather/DESCRIPTION
Updating namespace directives
Writing chinaWeather.Rd
Writing filename.Rd
```

### 查看生成的latex文件

```{base}
~ cat /home/conan/R/chinaWeather/man/filename.Rd

\name{filename}
\alias{filename}
\title{Define a filename from current date.}
\usage{
  filename(date = Sys.time())
}
\arguments{
  \item{date}{input a date type}
}
\value{
  character a file name
}
\description{
  Define a filename from current date.
}
\examples{
filename()
filename(as.Date("20110701",format="\%Y\%m\%d"))
}
\keyword{filename}
```

## 6. 程序打包

详细的打包解释，请参考：[开发自己的R包sayHello](http://blog.fens.me/r-build-package/)

对上面程序过程，更简化操作可以用如下3条命令

```{bash}
> load_all("/home/conan/R/chinaWeather")
> test("/home/conan/R/chinaWeather")
> document("/home/conan/R/chinaWeather")
```

### 程序检查

```{bash}
> check("/home/conan/R/chinaWeather")
Updating chinaWeather documentation
Loading chinaWeather
'/usr/lib/R/bin/R' --vanilla CMD build '/home/conan/R/chinaWeather'  \
  --no-manual --no-resave-data

* checking for file '/home/conan/R/chinaWeather/DESCRIPTION' ... OK
* preparing 'chinaWeather':
* checking DESCRIPTION meta-information ... OK
* checking for LF line-endings in source and make files
* checking for empty or unneeded directories
* building 'chinaWeather_0.1.tar.gz'

'/usr/lib/R/bin/R' --vanilla CMD check  \
  '/tmp/RtmpM5NdJp/chinaWeather_0.1.tar.gz' --timings

* using log directory '/tmp/RtmpM5NdJp/chinaWeather.Rcheck'
* using R version 3.0.1 (2013-05-16)
* using platform: x86_64-pc-linux-gnu (64-bit)
* using session charset: ASCII
* checking for file 'chinaWeather/DESCRIPTION' ... OK
* checking extension type ... Package
* this is package 'chinaWeather' version '0.1'
* checking package namespace information ... OK
* checking package dependencies ... OK
* checking if this is a source package ... OK
* checking if there is a namespace ... OK
* checking for executable files ... OK
* checking for hidden files and directories ... OK
* checking for portable file names ... OK
* checking for sufficient/correct file permissions ... OK
* checking whether package 'chinaWeather' can be installed ... OK
* checking installed package size ... OK
* checking package directory ... OK
* checking DESCRIPTION meta-information ... OK
* checking top-level files ... OK
* checking for left-over files ... OK
* checking index information ... OK
* checking package subdirectories ... OK
* checking R files for non-ASCII characters ... OK
* checking R files for syntax errors ... OK
* checking whether the package can be loaded ... OK
* checking whether the package can be loaded with stated dependencies ... OK
* checking whether the package can be unloaded cleanly ... OK
* checking whether the namespace can be loaded with stated dependencies ... OK
* checking whether the namespace can be unloaded cleanly ... OK
* checking loading without being on the library search path ... OK
* checking for unstated dependencies in R code ... OK
* checking S3 generic/method consistency ... OK
* checking replacement functions ... OK
* checking foreign function calls ... OK
* checking R code for possible problems ... OK
* checking Rd files ... OK
* checking Rd metadata ... OK
* checking Rd cross-references ... OK
* checking for missing documentation entries ... OK
* checking for code/documentation mismatches ... OK
* checking Rd \usage sections ... OK
* checking Rd contents ... OK
* checking for unstated dependencies in examples ... OK
* checking examples ... OK
* checking PDF version of manual ... OK

Checking chinaWeather with devtools
Checking for any extra files in built .tar.gz file... OK
```

查检通过.

注：请安装

```{bash}
sudo apt-get install texlive-full
```

## 7. 程序发布

我们把写的程序发布到github上面，然后通过devtools，我可以方便的把程序从github下载安装。

在github创建一个新的资源库：chinaWeather

https://github.com/bsspirit/chinaWeather

提交本地代码到github

```{bash}
~ cd /home/conan/R/chinaWeather
~ git init
~ git add .
~ git commit -m 'init commit'
~ git remote add origin https://github.com/bsspirit/chinaWeather
~ git push -u origin master

To https://github.com/bsspirit/chinaWeather
 * [new branch]      master -> master
Branch master set up to track remote branch master from origin.
```

### 通过devtools下载，并安装代码。

现在我的chinaWeather包，已经在github上面发布了，如果其他的同学想使用可以下面命令安装。

```{bash}
> library(devtools)
> install_github("chinaWeather","bsspirit")
Installing github repo(s) chinaWeather/master from bsspirit
Downloading chinaWeather.zip from https://github.com/bsspirit/chinaWeather/archive/master.zip
Installing package from /tmp/RtmpSaXYcA/chinaWeather.zip
Installing chinaWeather
'/usr/lib/R/bin/R' --vanilla CMD INSTALL  \
  '/tmp/RtmpSaXYcA/chinaWeather-master'  \
  --library='/home/conan/R/x86_64-pc-linux-gnu-library/3.0'  \
  --with-keep.source --install-tests

* installing *source* package 'chinaWeather' ...
** R
** inst
** preparing package for lazy loading
** help
*** installing help indices
** building package indices
** testing if installed package can be loaded
* DONE (chinaWeather)

#测试包
> library(chinaWeather)
> filename()
[1] "20130805.csv"

#查看文档
> ?filename
filename             package:chinaWeather              R Documentation
Define a filename from current date.
Description:
     Define a filename from current date.
Usage:
       filename(date = Sys.time())
Arguments:
    date: input a date type
Value:
     character a file name
Examples:
     filename()
     filename(as.Date("20110701",format="%Y%m%d"))
```

我们完成了，开发R包的全部流程。依赖于devtools, roxygen2, testthat三个包，真是事半功倍，比起完全手动操作提高效率了很多！！

希望更多的朋友，可以站在巨人的肩膀前行，创造让人惊叹的成果来！！

#### 转载请注明出处：http://blog.fens.me/r-package-faster/


