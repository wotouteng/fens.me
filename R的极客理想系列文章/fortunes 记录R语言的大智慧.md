fortunes 记录R语言的大智慧
=========

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-fortunes/

![fortunes 记录R语言的大智慧](http://blog.fens.me/wp-content/uploads/2013/11/fortunes-r.png)

#### 前言

作为R语言的使用者，有谁了解更R语言本身呢？他的起源，成长，经历？为何会有如此多的开发者，贡献者？

R语言是在大数据时代被炒作起来后，映入世人眼球。但R语言的社区已经存在很多年，我们并不知道，R语言在很长的历史时期，有着什么样的智慧。不过，正有人悄悄地记录着R语言的大智慧。

#### 目录

1. fortunes介绍
2. fortunes安装
3. fortunes使用

## 1. fortunes介绍

fortunes库是一个R语言的语录集，截止到2012年4月，一共总结了316条R-help的留言，这些都是R语言智慧的精华。让R语言的后辈使用者，可以更了解R语言的本身，了解R的精神。

fortunes项目发布页：http://cran.r-project.org/web/packages/fortunes/index.html

## 2. fortunes安装

系统环境：

* Linux: Ubuntu 12.04.2 LTS 64bit
* R: 3.0.1 x86_64-pc-linux-gnu

### fortunes安装

```{bash}

~ R

> install.packages("fortunes")
> library(fortunes)

#查看帮助
> ?fortunes
```

## 3. fortunes使用

### 随机看一条语录

```{bash}
> fortune()

Barry Rowlingson: Your grid above has 8*6 = 42 points.
(That was a subtle Hitchhikers Guide To The Galaxy reference there, honest, and
not a stupid dumb multiplication mistake on my part after working four 18-hour
days on the trot...)
Peter Dalgaard: [...] Don't panic, just throw yourself at the ground and miss.
   -- Barry Rowlingson and Peter Dalgaard
      R-help (March 2004)
```

### 指定查看语录

```{bash}
> fortune(108)

Actually, I see it as part of my job to inflict R on people who are perfectly
happy to have never heard of it. Happiness doesn't equal proficient and
efficient. In some cases the proficiency of a person serves a greater good than
their momentary happiness.
   -- Patrick Burns
      R-help (April 2005)
```

完整的语录下载：http://cran.r-project.org/web/packages/fortunes/vignettes/fortunes.pdf

把心静下来，想用好一门语言，需要更深入的了解。也许10年后，你的话也会被后人读起！

#### 转载请注明出处：http://blog.fens.me/r-fortunes/

