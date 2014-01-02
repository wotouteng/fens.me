R语言性能可视化lineprof
=======

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-perform-lineprof/

![R语言性能可视化lineprof](http://blog.fens.me/wp-content/uploads/2013/11/lineprof-r.png)

#### 前言

数据可视化越来越受到人们的追捧，图形比文字更有表达力，基于HTML的可交互的图形比静态的PNG图片更让人惊喜。R语言已经为数据可视化做了充分的准备，简简单单地几行代码，就可以让数据变成图片，让图片变成会动的图片。

本文以“性能报告”为切入点，讲述R语言可视化程序lineprof。

#### 目录

1. lineprof介绍
2. lineprof安装
3. lineprof使用
4. lineprof的API介绍

## 1. lineprof介绍

lineprof是一个数据可视化的项目，为了更友好地可视化性能监控效果。[上文中通过profr库](http://blog.fens.me/r-perform-rprof-profr/)，可以把性能数据以图片的形式输出，但缺点是仅仅是一张静态图片。而lineprof可以做的更好，生成基于shiny的可交互的网页，让你自己动手发现问题。

lineprof项目，又是“Hadley Wickham”大神的作品。目前项目只在github上面发布。github项目地址：https://github.com/hadley/lineprof

## 2. lineprof安装

### 系统环境

* Linux: Ubuntu Server 12.04.2 LTS 64bit
* R: 3.0.1 x86_64-pc-linux-gnu
* RStudio Server online

由于项目lineprof，还没有发布到CRAN，仅支持github安装。我们要通过devtools来通过github来安装。

安装devtools工具

```{bash}
~ R
> install.packages("devtools")
```

devtools的详细安装说明和使用，请参考文章：[在巨人的肩膀前行 催化R包开发](http://blog.fens.me/r-package-faster/)

### 安装lineprof

```{bash}
> library(devtools)

> install_github("lineprof")
Installing github repo(s) lineprof/master from hadley
Downloading lineprof.zip from https://github.com/hadley/lineprof/archive/master.zip
Installing package from /tmp/RtmpnLt4fi/lineprof.zip
Installing lineprof
Installing dependencies for lineprof:
Rcpp
Installing package into ‘/home/conan/R/x86_64-pc-linux-gnu-library/3.0’
(as ‘lib’ is unspecified)
trying URL 'http://cran.rstudio.com/src/contrib/Rcpp_0.10.6.tar.gz'
Content type 'application/x-gzip' length 1985569 bytes (1.9 Mb)
opened URL
==================================================
downloaded 1.9 Mb

* installing *source* package ‘Rcpp’ ...
** package ‘Rcpp’ successfully unpacked and MD5 sums checked
** libs
g++ -I/usr/share/R/include -DNDEBUG -I../inst/include/     -fpic  -O3 -pipe  -g  -c Date.cpp -o Date.o
g++ -I/usr/share/R/include -DNDEBUG -I../inst/include/     -fpic  -O3 -pipe  -g  -c Module.cpp -o Module.o
gcc -std=gnu99 -I/usr/share/R/include -DNDEBUG -I../inst/include/     -fpic  -O3 -pipe  -g  -c Rcpp_init.c -o Rcpp_init.o
g++ -I/usr/share/R/include -DNDEBUG -I../inst/include/     -fpic  -O3 -pipe  -g  -c Timer.cpp -o Timer.o
g++ -I/usr/share/R/include -DNDEBUG -I../inst/include/     -fpic  -O3 -pipe  -g  -c api.cpp -o api.o
g++ -I/usr/share/R/include -DNDEBUG -I../inst/include/     -fpic  -O3 -pipe  -g  -c attributes.cpp -o attributes.o
g++ -I/usr/share/R/include -DNDEBUG -I../inst/include/     -fpic  -O3 -pipe  -g  -c barrier.cpp -o barrier.o
g++ -I/usr/share/R/include -DNDEBUG -I../inst/include/     -fpic  -O3 -pipe  -g  -c exceptions.cpp -o exceptions.o
g++ -shared -o Rcpp.so Date.o Module.o Rcpp_init.o Timer.o api.o attributes.o barrier.o exceptions.o -L/usr/lib/R/lib -lR
g++ -o libRcpp.so Date.o Module.o Rcpp_init.o Timer.o api.o attributes.o barrier.o exceptions.o -shared   -L/usr/lib/R/lib -lR
ar qc libRcpp.a Date.o Module.o Rcpp_init.o Timer.o api.o attributes.o barrier.o exceptions.o
cp libRcpp.so ../inst/lib
cp libRcpp.a ../inst/lib
rm libRcpp.so libRcpp.a
installing to /home/conan/R/x86_64-pc-linux-gnu-library/3.0/Rcpp/libs
** R
** inst
** preparing package for lazy loading
** help
*** installing help indices
** building package indices
** installing vignettes
** testing if installed package can be loaded
* DONE (Rcpp)

The downloaded source packages are in
	‘/tmp/RtmpnLt4fi/downloaded_packages’
'/usr/lib/R/bin/R' --vanilla CMD INSTALL '/tmp/RtmpnLt4fi/lineprof-master'  \
  --library='/home/conan/R/x86_64-pc-linux-gnu-library/3.0'  \
  --with-keep.source --install-tests 

* installing *source* package 'lineprof' ...
** libs
g++ -I/usr/share/R/include -DNDEBUG   -I"/home/conan/R/x86_64-pc-linux-gnu-library/3.0/Rcpp/include"   -fpic  -O3 -pipe  -g  -c RcppExports.cpp -o RcppExports.o
g++ -I/usr/share/R/include -DNDEBUG   -I"/home/conan/R/x86_64-pc-linux-gnu-library/3.0/Rcpp/include"   -fpic  -O3 -pipe  -g  -c contains.cpp -o contains.o
g++ -I/usr/share/R/include -DNDEBUG   -I"/home/conan/R/x86_64-pc-linux-gnu-library/3.0/Rcpp/include"   -fpic  -O3 -pipe  -g  -c parse-refs.cpp -o parse-refs.o
g++ -shared -o lineprof.so RcppExports.o contains.o parse-refs.o -L/home/conan/R/x86_64-pc-linux-gnu-library/3.0/Rcpp/lib -lRcpp -Wl,-rpath,/home/conan/R/x86_64-pc-linux-gnu-library/3.0/Rcpp/lib -L/usr/lib/R/lib -lR
installing to /home/conan/R/x86_64-pc-linux-gnu-library/3.0/lineprof/libs
** R
** inst
** preparing package for lazy loading
** help
*** installing help indices
** building package indices
** testing if installed package can be loaded
* DONE (lineprof)
```

### 安装shiny控制台

```{bash}
> install_github("shiny-slickgrid", "wch")
```

### 加载lineprof

```{bash}
> library(lineprof)
```

## 3. lineprof使用

### 官文例子

```{bash}
> library(lineprof)
> source(find_ex("read-delim.r"))
> wine <- find_ex("wine.csv")

> x <- lineprof(read_delim(wine, sep = ","), torture = TRUE)
Zooming to read-delim.r (99% of total time)

> x <- lineprof(read_delim(wine, sep = ","), torture = TRUE)
Zooming to read-delim.r (97% of total time)
```

### 用shiny展示

```{bash}
> library(shinySlickgrid)
> shine(x)
Loading required package: shiny
Shiny URLs starting with /lineprof will mapped to /home/conan/R/x86_64-pc-linux-gnu-library/3.0/lineprof/www
Shiny URLs starting with /slickgrid will mapped to /home/conan/R/x86_64-pc-linux-gnu-library/3.0/shinySlickgrid/slickgrid

Listening on port 6742
```

![](http://blog.fens.me/wp-content/uploads/2013/11/lineprof-shiny.png)

### 6列表格字段的解释：

* #: 行号
* source code: 监控的目标函数源代码
* t: (total)当前行执行的总时间(秒)
* r: (released) 释放内存量
* a: (allocated) 分配内存量
* d: (duplicates)重复次数

### 对函数性能数据的解释:

* #6，用于加载数据，总时间占用:0.309s，分配内存：0.064mb，方法重复次数:14次
* #15，用于清除数据，总时间占用:0.179s，释放内存：0.065mb，方法重复次数:37次

### 对例子资源的介绍：

* read-delim.r: 目标函数的脚本文件
* wine.csv: 测试数据集
* x: lineprof生成的数据报告

### 查看文件:read-delim.r

```{bash}
function(file, header = TRUE, sep = ",", stringsAsFactors = TRUE) {
  # Determine number of fields by reading first line
  first <- scan(file, what = character(1), nlines = 1, sep = sep, quiet = TRUE)
  p <- length(first)
    
  # Load all fields
  all <- scan(file, what = as.list(rep("character", p)), sep = sep, 
    skip = if (header) 1 else 0, quiet = TRUE)

  # Convert from strings to appropriate types
  all[] <- lapply(all, type.convert, as.is = !stringsAsFactors)
  
  # Set column names
  if (header) {
    names(all) <- first
    rm(first)
  } else {
    names(all) <- paste0("V", seq_along(all))
  }
    
  # Convert list into data frame
  class(all) <- "data.frame"
  attr(all, "row.names") <- c(NA_integer_, -length(all[[1]]))
    
  all
}
```

### 查看文件:wine.csv

```{bash}
> df<-read.csv(file=wine)

> object.size(df)
20440 bytes

> head(df)
  type alcohol malic  ash alcalinity magnesium phenols flavanoids nonflavanoids proanthocyanins color  hue dilution proline
1    A   14.23  1.71 2.43       15.6       127    2.80       3.06          0.28            2.29  5.64 1.04     3.92    1065
2    A   13.20  1.78 2.14       11.2       100    2.65       2.76          0.26            1.28  4.38 1.05     3.40    1050
3    A   13.16  2.36 2.67       18.6       101    2.80       3.24          0.30            2.81  5.68 1.03     3.17    1185
4    A   14.37  1.95 2.50       16.8       113    3.85       3.49          0.24            2.18  7.80 0.86     3.45    1480
5    A   13.24  2.59 2.87       21.0       118    2.80       2.69          0.39            1.82  4.32 1.04     2.93     735
6    A   14.20  1.76 2.45       15.2       112    3.27       3.39          0.34            1.97  6.75 1.05     2.85    1450
```

### x对象: lineprof生成的数据报告

```{bash}

> x
Reducing depth to 2 (from 8)
Common path: 
    time alloc release dups      ref               src
1  0.002 0.001   0.000    0  #3 read_delim       
2  0.049 0.009   0.003   11  #3 read_delim/scan  
3  0.026 0.001   0.008    0  #4 read_delim       
4  0.379 0.072   0.006   14  #7 read_delim/scan  
5  0.003 0.000   0.000    0 #11 read_delim       
6  0.106 0.015   0.030    3 #11 read_delim/lapply
7  0.008 0.004   0.000    3 #11 read_delim       
8  0.210 0.028   0.077   36 #16 read_delim/rm    
9  0.004 0.001   0.000    1 #22 read_delim       
10 0.035 0.005   0.004    8 #23 read_delim/[[    
11 0.002 0.000   0.000    1 #23 read_delim/length
12 0.001 0.000   0.000    1 #23 read_delim/c     
13 0.006 0.004   0.000    1 #23 read_delim       
14 0.001 0.000   0.000    0 #23 read_delim/attr<-
```

## 4. lineprof的API介绍

通过[namespace文件](https://github.com/hadley/lineprof/blob/master/NAMESPACE)定义，我们找到lineprof库开放的API函数。

功能函数：使用者的API

* focus: 设置显示高度zoom
* auto_focus: 自动设置显示高度zoom
* lineprof: 记录CPU和内存的占用
* shine: 用shiny输出

内部函数：辅助功能函数的，使用者用不到的API

* align: 源代码对齐
* find_ex: 用于加载demo
* line_profile: 格式化性能监控数据的输出(Rprof)
* parse_prof: 格式化输出
* reduce_depth: 设置输出的深度

通过lineprof可视化工具，我们生成的报告，更灵活，更直观，更好看，还有用户交互，甚至是网页版本的。
再和profr效果比较一下吧，[R语言性能监控工具Rprof](http://blog.fens.me/r-perform-rprof-profr/)

R语言正在飞速地进步着，需要更多IT人的推动！！你还在等什么呢？

#### 转载请注明出处：http://blog.fens.me/r-perform-lineprof/

