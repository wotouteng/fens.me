plot.xts时间序列可视化
============

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-xts-xtsextra/

![plot.xts时间序列可视化](http://blog.fens.me/wp-content/uploads/2013/11/xtsExtra-r.png)

#### 前言

r-bloggers的[一篇博文](http://www.r-bloggers.com/plot-xts-is-wonderful/)，让我有动力继续发现xts的强大。xts扩展了zoo的基础数据结构，并提供了更丰富的功能函数。xtsExtra补充库，从可视化的角度出发，提供了一个简单而效果非凡的作图函数plot.xts。

本文将用plot.xts来演示，xts对象的时间序列可视化！

#### 目录

1. xtsExtra介绍
2. xtsExtra安装
3. plot.xts的使用

## 1. xtsExtra介绍

xtsExtra是xts包的功能补充包，该软件包在Google Summer of Code 2012被开发，最终将合并到xts包。xtsExtra提供的主要功能就是plot.xts。

注：我发现xts::plot.xts的函数，与xtsExtra::plot.xts还是有差别的。

关于xts包的介绍，请参考文章：[可扩展的时间序列xts](http://blog.fens.me/r-xts/)

下面我们安装xtsExtra包。

## 2. xtsExtra安装

由于xtsExtra没有发布到CRAN，我们要从R-Forge下载。

```{bash}

~ R

> install.packages("xtsExtra", repos="http://R-Forge.R-project.org")
Warning in install.packages :
  package ‘xtsExtra’ is not available (for R version 3.0.1)
trying URL 'http://R-Forge.R-project.org/bin/windows/contrib/3.0/xtsExtra_0.0-1.zip'
Content type 'application/zip' length 242682 bytes (236 Kb)
opened URL
downloaded 236 Kb

package ‘xtsExtra’ successfully unpacked and MD5 sums checked

The downloaded binary packages are in
	C:\Users\Administrator\AppData\Local\Temp\Rtmp04stLd\downloaded_packages
```

加载xtsExtra

```{bash}
> library(xtsExtra)
载入需要的程辑包：zoo

载入程辑包：‘zoo’

下列对象被屏蔽了from ‘package:base’:

    as.Date, as.Date.numeric

载入需要的程辑包：xts

载入程辑包：‘xtsExtra’

下列对象被屏蔽了from ‘package:xts’:

    plot.xts

Warning messages:
1: 程辑包‘zoo’是用R版本3.0.2 来建造的 
2: 程辑包‘xts’是用R版本3.0.2 来建造的 
```

plot.xts函数被用来，覆盖xts::plot.xts函数。

## 3. plot.xts的使用

* 1). plot.xts的参数列表
* 2). 简单的时间序列
* 3). K线图
* 4). panel配置
* 5). screens配置
* 6). events配置
* 7). 双时间序列
* 9). barplot

### 1). plot.xts的参数列表

```{bash}
> names(formals(plot.xts))
 [1] "x"              "y"              "screens"        "layout.screens" "..."           
 [6] "yax.loc"        "auto.grid"      "major.ticks"    "minor.ticks"    "major.format"  
[11] "bar.col.up"     "bar.col.dn"     "candle.col"     "xy.labels"      "xy.lines"      
[16] "ylim"           "panel"          "auto.legend"    "legend.names"   "legend.loc"    
[21] "legend.pars"    "events"         "blocks"         "nc"             "nr"       
```

### 2). 简单的时间序列

```{bash}
> data(sample_matrix)
> sample_xts <- as.xts(sample_matrix)
> plot(sample_xts[,1]) 
> class(sample_xts[,1])
[1] "xts" "zoo"
```

![](http://blog.fens.me/wp-content/uploads/2013/11/plot.xts-basic.png)

### 3). K线图

红白色

```{bash}
> plot(sample_xts[1:30, ], type = "candles")
```

![](http://blog.fens.me/wp-content/uploads/2013/11/plot.xts-red.png)

自定义颜色

```{bash}
> plot(sample_xts[1:30, ], type = "candles", bar.col.up = "blue", bar.col.dn = "violet", candle.col = "green4")
```

![](http://blog.fens.me/wp-content/uploads/2013/11/plot.xts-col.png)

### 4). panel配置

基本面板

```{bash}
> plot(sample_xts[,1:2]) 
```

![](http://blog.fens.me/wp-content/uploads/2013/11/plot.xts-panel.png)

多行面板

```{bash}
> plot(sample_xts[,rep(1:4, each = 3)]) 
```

![](http://blog.fens.me/wp-content/uploads/2013/11/plot.xts-mpanel.png)

自由组合面板

```{bash}
> plot(sample_xts[,1:4], layout.screens = matrix(c(1,1,1,1,2,3,4,4),ncol = 2, byrow = TRUE))
```

![](http://blog.fens.me/wp-content/uploads/2013/11/plot.xts-cpanel.png)

### 5). screens配置

双屏幕显示，每屏幕2条线

```{bash}
> plot(sample_xts, screens = 1:2) 
```

![](http://blog.fens.me/wp-content/uploads/2013/11/plot.xts-screen.png)

双屏幕显示，指定曲线出现的屏幕和颜色

```{bash}
> plot(sample_xts, screens = c(1,2,1,2), col = c(1,3,2,2))
```

![](http://blog.fens.me/wp-content/uploads/2013/11/plot.xts-screen-4.png)

双屏幕显示，指定不同的坐标系

```{bash}
> plot(10^sample_xts, screens = 1:2, log= c("","y"))
```

![](http://blog.fens.me/wp-content/uploads/2013/11/plot.xts-screen-axis.png)

双屏幕显示，指定不同的输出图形

```{bash}
> plot(sample_xts[1:75,1:2] - 50.5, type = c("l","h"), lwd = c(1,2))
```

![](http://blog.fens.me/wp-content/uploads/2013/11/plot.xts-screen-chart.png)

多屏幕，分组显示

```{bash}
> plot(sample_xts[,c(1:4, 3:4)], layout = matrix(c(1,1,1,1,2,2,3,4,5,6), ncol = 2, byrow = TRUE), yax.loc = "left")
```

![](http://blog.fens.me/wp-content/uploads/2013/11/plot.xts-screen-mg.png)

### 6). events配置

基本事件分割线

```{bash}
> plot(sample_xts[,1], events = list(time = c("2007-03-15","2007-05-01"), label = "bad days"), blocks = list(start.time = c("2007-03-05", "2007-04-15"), end.time = c("2007-03-20","2007-05-30"), col = c("lightblue1", "lightgreen")))
```

![](http://blog.fens.me/wp-content/uploads/2013/11/plot.xts-event.png)

### 7). 双时间序列

双坐标视图

```{bash}
> plot(sample_xts[,1],sample_xts[,2])
```

![](http://blog.fens.me/wp-content/uploads/2013/11/plot.xts-scatter.png)

双坐标梯度视图

```{bash}
> cr <- colorRampPalette(c("#00FF00","#FF0000"))
> plot(sample_xts[,1],sample_xts[,2], xy.labels = FALSE, xy.lines = TRUE, col = cr(NROW(sample_xts)), type = "l")
```

![](http://blog.fens.me/wp-content/uploads/2013/11/plot.xts-gradient.png)

### 8). xts类型转换作图

ts类型作图

```{bash}
> tser <- ts(cumsum(rnorm(50, 0.05, 0.15)), start = 2007, frequency = 12)
> class(tser)
[1] "ts"
> plot(tser)
```

![](http://blog.fens.me/wp-content/uploads/2013/11/plot.xts-ts.png)

以xts类型作图

```{bash}
> plot.xts(tser)
```

![](http://blog.fens.me/wp-content/uploads/2013/11/plot.xts-ts-xts.png)

### 9). barplot

```{bash}
> x <- xts(matrix(abs(rnorm(72)), ncol = 6), Sys.Date() + 1:12)
> colnames(x) <- LETTERS[1:6]
> barplot(x)
```

![](http://blog.fens.me/wp-content/uploads/2013/11/plot.xts-barplot.png)

我们看到xtsExtra::plot.xts提供了强大的作图功能，很容易做出可视的时间序列！

#### 转载请注明出处：http://blog.fens.me/r-xts-xtsextra/


