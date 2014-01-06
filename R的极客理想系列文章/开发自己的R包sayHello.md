开发自己的R包sayHello
=======

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-build-package/

![开发自己的R包sayHello](http://blog.fens.me/wp-content/uploads/2013/08/r-package.png)

#### 前言
R是一个世界范围开发者共同协作的产物，至2013年2月共计近5000个包可在互联网上自由下载。现在作为R的使用者，有朝一日也可以成为R的开发者，把我们自己的知识做成R包分享给世界。

今天我们简单介绍如何开发自己R包。

#### 目录

1. 系统环境
2. vi中制作R包
3. RStudio中制作R包

## 1. 系统环境

* 操作系统：Linux Ubuntu 12.04.2 LTS 64bit
* R语言版本：R 3.0.1

```{bash}
~ uname -a
Linux conan-deskop 3.5.0-23-generic #35~precise1-Ubuntu SMP Fri Jan 25 17:13:26 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux

~ cat /etc/issue
Ubuntu 12.04.2 LTS \n \l

~ R --version
R version 3.0.1 (2013-05-16) -- "Good Sport"
Copyright (C) 2013 The R Foundation for Statistical Computing
Platform: x86_64-pc-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under the terms of the
GNU General Public License versions 2 or 3.
For more information about these matters see

http://www.gnu.org/licenses/.
```

## 2. vi中制作R包

### 创建目录：/home/conan/R/demo

```{bash}
~ mkdir /home/conan/R/demo
~ cd /home/conan/R/demo
```

### 新建R脚本sayHello.R

```{bash}
~ vi sayHello.R
sayHello<-function(name){
    print(paste("Hello",name))
}
```

自定义一个sayHello的函数，将作为自定义包的第一个函数。

### 启动R程序

```{bash}
~ R

#清空变量设置工作目录
rm(list=ls())
setwd("/home/conan/R/demo")

#通过sayHello的脚本生成开发包的骨架
package.skeleton(name="sayHello",code_files="/home/conan/R/demo/sayHello.R")
Creating directories ...
Creating DESCRIPTION ...
Creating NAMESPACE ...
Creating Read-and-delete-me ...
Copying code files ...
Making help files ...
Done.
Further steps are described in './sayHello/Read-and-delete-me'.

#退出R的客户端
q()
```

### 在当前目录生成sayHello目录

```{bash}
~ ls -l
drwxrwxr-x 4 conan conan 4096  8月  1 15:07 sayHello
-rw-rw-r-- 1 conan conan   59  8月  1 15:04 sayHello.R

~ ls -l sayHello
-rw-rw-r-- 1 conan conan  281  8月  1 15:07 DESCRIPTION
drwxrwxr-x 2 conan conan 4096  8月  1 15:07 man
-rw-rw-r-- 1 conan conan   31  8月  1 15:07 NAMESPACE
drwxrwxr-x 2 conan conan 4096  8月  1 15:07 R
-rw-rw-r-- 1 conan conan  420  8月  1 15:07 Read-and-delete-me

~ ls -l sayHello/man
-rw-rw-r-- 1 conan conan 1043  8月  1 15:07 sayHello-package.Rd
-rw-rw-r-- 1 conan conan 1278  8月  1 15:07 sayHello.Rd

~ ls -l sayHello/R
-rw-rw-r-- 1 conan conan 59  8月  1 15:07 sayHello.R
```

### 文件及目录解释：

* DESCRIPTION文件: 包描述文件
* NAMESPACE文件: 包的命名空间文件
* Read-and-delete-me文件: 说明文件，可以删除
* man目录: 存放函数的说明文件的目录
* R目录：存放源文件的目录
* man/sayHello.Rd: sayHello函数的说明文件，latex语法，用来生成PDF文档
* man/sayHello-package.Rd: sayHello包的说明文件，可以删除

编辑DESCRIPTION文件：

```{bash}
~ vi sayHello/DESCRIPTION
Package: sayHello
Type: Package
Title: R package demo for sayHello
LazyLoad: yes
Author: Dan Zhang
Maintainer: Dan Zhang
Description: This package provides a package demo
License: GPL
Version: 1.0
Date: 2013-07-31
Depends: R (>= 3.0.1)
```

编辑NAMESPACE文件：

```{bash}
~ vi sayHello/NAMESPACE
exportPattern("^[[:alpha:]]+")
```

编辑sayHello.Rd文件：

```{bash}
~ vi sayHello/man/sayHello.Rd
\name{sayHello}
\alias{sayHello}
\title{a sayHello function demo}
\description{
a sayHello function demo
}
\usage{
sayHello(name)
}
\arguments{
  \item{name}{a word}
}
\details{
nothing
}
\value{
no return
}
\references{
nothing
}
\author{
Dan Zhang
}
\note{
nothing
}
\seealso{
nothing
}
\examples{
sayHello("world")
}
\keyword{ sayHello }
```

删除文件：

```{bash}
~ rm sayHello/Read-and-delete-me
~ rm sayHello/man/sayHello-package.Rd
```

### 打包sayHello

```{bash}
~ R CMD build sayHello
* checking for file ‘sayHello/DESCRIPTION’ ... OK
* preparing ‘sayHello’:
* checking DESCRIPTION meta-information ... OK
* checking for LF line-endings in source and make files
* checking for empty or unneeded directories
* building ‘sayHello_1.0.tar.gz’

~ ls -l
drwxrwxr-x 4 conan conan 4096  8月  1 15:22 sayHello
-rw-r--r-- 1 conan conan  663  8月  1 15:24 sayHello_1.0.tar.gz
-rw-rw-r-- 1 conan conan   59  8月  1 15:04 sayHello.R
```

### 本地安装sayHello

```{bash}
~ R CMD INSTALL sayHello_1.0.tar.gz
* installing to library ‘/home/conan/R/x86_64-pc-linux-gnu-library/3.0’
* installing *source* package ‘sayHello’ ...
** R
** preparing package for lazy loading
** help
*** installing help indices
** building package indices
** testing if installed package can be loaded
* DONE (sayHello)

~ ls /home/conan/R/x86_64-pc-linux-gnu-library/3.0
bitops  digest  manipulate  rstudio  sayHello  XML
```

sayHello包，已经在当前账号下进行安装

### 使用sayHello函数

```{bash}
~ R

library(sayHello)
sayHello("Conan")
[1] "Hello Conan"

#查看sayHello文档
?sayHello

sayHello               package:sayHello                R Documentation

a sayHello function demo

Description:
     a sayHello function demo
Usage:
     sayHello(name)
Arguments:
    name: a word
Details:
     nothing
Value:
     no return
Note:
     nothing
Author(s):
     Dan Zhang
References:
     nothing
```

我们包已经成功制作完成！并在本地进行安装和使用！！

### 检查R包

如果要提交R包和CRAN，必须要执行check检查。如果有任何的error和warning都将不被通过。

退出R的客户端,回到命令行

先安装latex的依赖包(500mb+)

```{bash}
~ sudo apt-get install texlive
~ sudo apt-get install texlive-xetex
~ sudo apt-get install texlive-latex-base
~ sudo apt-get install texlive-fonts-extra 
~ sudo apt-get install texlive-latex-recommended
~ sudo apt-get install texlive-fonts-recommended
```

### 执行check

```{bash}
~ R CMD check sayHello_1.0.tar.gz

* using log directory ‘/home/conan/R/demo/sayHello.Rcheck’
* using R version 3.0.1 (2013-05-16)
* using platform: x86_64-pc-linux-gnu (64-bit)
* using session charset: UTF-8
* checking for file ‘sayHello/DESCRIPTION’ ... OK
* checking extension type ... Package
* this is package ‘sayHello’ version ‘1.0’
* checking package namespace information ... OK
* checking package dependencies ... OK
* checking if this is a source package ... OK
* checking if there is a namespace ... OK
* checking for executable files ... OK
* checking for hidden files and directories ... OK
* checking for portable file names ... OK
* checking for sufficient/correct file permissions ... OK
* checking whether package ‘sayHello’ can be installed ... OK
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
* checking PDF version of manual ... WARNING
LaTeX errors when creating PDF version.
This typically indicates Rd problems.
* checking PDF version of manual without hyperrefs or index ... OK

WARNING: There was 1 warning.
See
  ‘/home/conan/R/demo/sayHello.Rcheck/00check.log’
for details.
```

发现有一个警告，checking PDF version of manual ... WARNING。是latex生成pdf错误。

### 查检警告的debug日志

```{bash}
~ R CMD Rd2pdf sayHello_1.0.tar.gz
Converting Rd files to LaTeX ...
  sayHello_1.0.tar.gz
Warning in readLines(f) :
  incomplete final line found on 'sayHello_1.0.tar.gz'
Warning in parse_Rd("sayHello_1.0.tar.gz", encoding = "unknown", fragment = FALSE,  :
  sayHello_1.0.tar.gz:16: unexpected '}'
Warning: sayHello_1.0.tar.gz:1: All text must be in a section
Warning: sayHello_1.0.tar.gz:2: All text must be in a section
Warning: sayHello_1.0.tar.gz:3: All text must be in a section
Warning: sayHello_1.0.tar.gz:4: All text must be in a section
Warning: sayHello_1.0.tar.gz:5: All text must be in a section
Warning: sayHello_1.0.tar.gz:6: All text must be in a section
Warning: sayHello_1.0.tar.gz:7: All text must be in a section
Warning: sayHello_1.0.tar.gz:8: All text must be in a section
Warning: sayHello_1.0.tar.gz:9: All text must be in a section
Warning: sayHello_1.0.tar.gz:10: All text must be in a section
Warning: sayHello_1.0.tar.gz:11: All text must be in a section
Warning: sayHello_1.0.tar.gz:12: All text must be in a section
Warning: sayHello_1.0.tar.gz:15: All text must be in a section
Error : sayHello_1.0.tar.gz: Sections \title, and \name must exist and be unique in Rd files
```

### 求助：

Error : sayHello_1.0.tar.gz: Sections \title, and \name must exist and be unique in Rd files

### 这个问题我检查了好多次始终没有解决。请求帮助！！

```{bash}
sudo apt-get install texlive-full
```

### 上传R包

当解决了上面的WARN就可以上传了。。。(待续)

### 卸载R包

```{bash}
remove.packages("sayHello")
Removing package from ‘/home/conan/R/x86_64-pc-linux-gnu-library/3.0’

~ ls /home/conan/R/x86_64-pc-linux-gnu-library/3.0
bitops  digest  manipulate  rstudio  XML
```

## 3. RStudio中制作R包

RStudio Server的安装及配置，请参考：[多人在线协作R开发RStudio](http://blog.fens.me/r-rstudio-server/) Server

RStudio已经为我们准备了一个有界面的环境，方便我们制作R包。

* 1. 创建一个工程sayHello
* 2. 编辑DESCRIPTION
* 3. 创建sayHello.R的脚本
* 4. 编辑sayHello.Rd的文档

### 1). 创建一个工程sayHello

![](http://blog.fens.me/wp-content/uploads/2013/08/project1.png)

![](http://blog.fens.me/wp-content/uploads/2013/08/project2.png)

### 2). 编辑rstudio/sayHello/DESCRIPTION

```{bash}
Package: sayHello
Type: Package
Title: R package demo for sayHell
LazyLoad: yes
Author: Dan Zhang
Maintainer: Dan Zhang
Description: This package provides a package demo
License: GPL
Version: 1.0
Date: 2013-07-31
Depends: R (>= 3.0.1)
```

### 3). 编辑rstudio/sayHello/R/sayHello.R的脚本

```{bash}
sayHello<-function(name){
  print(paste("Hi",name))
}
```

### 4). 编辑rstudio/sayHello/man/sayHello.Rd的文档

```{bash}
\name{sayHello}
\alias{sayHello}
\title{a sayHello function demo}
\description{
a sayHello function demo
}
\usage{
sayHello(name)
}
\arguments{
  \item{name}{a word}
}
\details{
nothing
}
\value{
no return
}
\references{
nothing
}
\author{
Dan Zhang
}
\note{
nothing
}
\seealso{
nothing
}
\examples{
sayHello("world")
}
\keyword{ sayHello }
```

### 5). 执行build和reload

![](http://blog.fens.me/wp-content/uploads/2013/08/project3.png)

![](http://blog.fens.me/wp-content/uploads/2013/08/project4.png)

### 6). 执行check

![](http://blog.fens.me/wp-content/uploads/2013/08/project5.png)

在RStudio中可以更方便的进行R的开包，打包，检查等的过程。RStudio就是R的神器！

虽然还没有发布自己的R包，不过很快了！！有此想法已经多时了！！每一天加油！

#### 转载请注明出处：http://blog.fens.me/r-build-package/

