R语言的高质量图形渲染库Cairo
======

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-cairo/

![R语言的高质量图形渲染库Cairo](http://blog.fens.me/wp-content/uploads/2013/11/r-cairo.png)

#### 前言

R语言不仅在统计分析，数据挖掘领域，计算能力强大。在数据可视化上，也不逊于昂贵的商业。当然，背后离不开各种开源软件包的支持，Cairo就是这样一个用于矢量图形处理的类库。

Cairo可以创建高质量的矢量图形(PDF, PostScript, SVG) 和 位图(PNG, JPEG, TIFF)，同时支持在后台程序中高质量渲染！

本文将介绍，Cairo在R语言中的使用。

#### 目录

1. Cairo介绍
2. Cairo安装
3. Cairo使用

## 1. Cairo介绍

在信息领域中，cairo 是一个让用于提供矢量图形绘图的免费库，cairo 提供在多个背景下做 2D 的绘图，高级的更可以使用硬件加速功能。

虽然 cairo 是使用C语言撰写的，但是当使用 cairo 时，可以用许多其他种语言来使用，包括有 C++、C#、Java、Python、Perl、Ruby、Scheme、Smalltalk 以及许多种语言，cairo 在 GNU LGPL 与 Mozilla Public License (MPL) 两个认证下发布。

上文介绍，摘自:http://zh.wikipedia.org/wiki/Cairo_(绘图)

R语言Cairo接口的官方发布页：http://www.rforge.net/Cairo/

## 2. Cairo安装

### 系统环境

* Linux: Ubuntu 12.04.2 LTS 64bit
* R: 3.0.1 x86_64-pc-linux-gnu

Cairo基本库安装

```{bash}

~ sudo apt-get install libcairo2-dev
~ sudo apt-get install libxt-dev

~ R 
> install.packages("Cairo")
** R
** preparing package for lazy loading
** help
*** installing help indices
** building package indices
** testing if installed package can be loaded
Warning: ignoring .First.lib() for package ‘Cairo’
* DONE (Cairo)
```

## 3. Cairo使用

Cairo使用起来非常简单，和基础包grDevices中的函数对应。

* CairoPNG: 对应grDevices:png()
* CairoJPEG: 对应grDevices:jpeg()
* CairoTIFF: 对应grDevices:tiff()
* CairoSVG: 对应grDevices:svg()
* CairoPDF: 对应grDevices:pdf()

我常用的图形输出，就是png和svg。

检查Cairo的兼容性：

```{bash}
~ R

> library(Cairo)
> Cairo.capabilities()
   png   jpeg   tiff    pdf    svg     ps    x11    win raster
  TRUE   TRUE  FALSE   TRUE   TRUE   TRUE   TRUE  FALSE   TRUE
```

下面比较一下 CairoPNG() 和 png() 输出效果。

### 1). 散点图

```{bash}
x<-rnorm(6000)
y<-rnorm(6000)

# PNG图
png(file="plot4.png",width=640,height=480)
plot(x,y,col="#ff000018",pch=19,cex=2,main = "plot")
dev.off()

CairoPNG(file="Cairo4.png",width=640,height=480)
plot(x,y,col="#ff000018",pch=19,cex=2,main = "Cairo")
dev.off()

# SVG图
svg(file="plot-svg4.svg",width=6,height=6)
plot(x,y,col="#ff000018",pch=19,cex=2,main = "plot-svg")
dev.off()

CairoSVG(file="Cairo-svg4.svg",width=6,height=6)
plot(x,y,col="#ff000018",pch=19,cex=2,main = "Cairo-svg")
dev.off()
```

以下为PNG图：

![](http://blog.fens.me/wp-content/uploads/2013/11/plot4.png)

![](http://blog.fens.me/wp-content/uploads/2013/11/Cairo4.png)

### 2). 三维截面图

```{bash}
x <- seq(-10, 10, length= 30)
y <- x
f <- function(x,y) { r <- sqrt(x^2+y^2); 10 * sin(r)/r }
z <- outer(x, y, f)
z[is.na(z)] <- 1

# PNG图
png(file="plot2.png",width=640,height=480)
op <- par(bg = "white", mar=c(0,2,3,0)+.1)
persp(x, y, z,
theta = 30, phi = 30,
expand = 0.5,
col = "lightblue",
ltheta = 120,
shade = 0.75,
ticktype = "detailed",
xlab = "X", ylab = "Y", zlab = "Sinc(r)",
main = "Plot"
)
par(op)
dev.off()

CairoPNG(file="Cairo2.png",width=640,height=480)
op <- par(bg = "white", mar=c(0,2,3,0)+.1)
persp(x, y, z,
theta = 30, phi = 30,
expand = 0.5,
col = "lightblue",
ltheta = 120,
shade = 0.75,
ticktype = "detailed",
xlab = "X", ylab = "Y", zlab = "Sinc(r)",
main = "Cairo"
)
par(op)
dev.off()
```

以下为PNG图：

![](http://blog.fens.me/wp-content/uploads/2013/11/plot2.png)

![](http://blog.fens.me/wp-content/uploads/2013/11/Cairo2.png)

### 3). 文字显示

```{bash}
library(MASS)
data(HairEyeColor)
x <- HairEyeColor[,,1]+HairEyeColor[,,2]

n <- 100
m <- matrix(sample(c(T,F),n^2,replace=T), nr=n, nc=n)

# PNG图
png(file="plot5.png",width=640,height=480)
biplot(corresp(m, nf=2), main="Plot")
dev.off()

CairoPNG(file="Cairo5.png",width=640,height=480)
biplot(corresp(m, nf=2), main="Cairo")
dev.off()

# SVG图
svg(file="plot-svg5.svg",width=6,height=6)
biplot(corresp(m, nf=2), main="Plot-svg")
dev.off()

CairoSVG(file="Cairo-svg5.svg",width=6,height=6)
biplot(corresp(m, nf=2), main="Cairo-svg")
dev.off()
```

以下为PNG图：

![](http://blog.fens.me/wp-content/uploads/2013/11/plot5.png)

![](http://blog.fens.me/wp-content/uploads/2013/11/Cairo5.png)

我们查看两个文件的属性：以png直接生成的图54KB，以CairoPNG生成的图43.8KB。

![](http://blog.fens.me/wp-content/uploads/2013/11/cairo-demo5.png)

综上的3个例子，我分辨不出太大区别，只是Cairo感觉更淡、更柔和一些。

大家不妨找一些更复杂的图形来尝试着比较。

#### 转载请注明出处：http://blog.fens.me/r-cairo/




