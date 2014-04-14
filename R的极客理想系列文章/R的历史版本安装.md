R的历史版本安装
==========

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-install-ubuntu/

![R的历史版本安装](http://blog.fens.me/wp-content/uploads/2014/03/r-install-history.png)


#### 前言

R语言软件包已进入到了3.0的时代，但有些第三方的R包还处于2.15的状态，没有及时升级，像RHadoop,RHive。我们要用这些R包的时候，就需要指定版本的R软件。

对于Windows来说，这是很简单的操作，只要安装不同的(exe)文件就行了；对于Linux系统，我们也需要手动的进行配置。不熟悉Linux同学，在这里就很容易卡住。所以，今天就讲一下，在Linux Ubuntu系统中，R语言软件包指定版本安装。

#### 目录

1. R在Windows中安装
2. R在Ubuntu中安装
3. R在Ubuntu中最新版本安装
4. R在Ubuntu中指定版本安装

## 1. R在Windows中安装

通过R的官方网站http://cran.r-project.org/，我们可以下载Linux, MacOS, Windows系统的R语言安装包。

R在Windows系统中安装非常简单，双击下载的可执行文件(.exe)，即可进行安装。

安装后，运行R语言的界面

![](http://blog.fens.me/wp-content/uploads/2014/03/r-win.png)

## 2. R在Ubuntu中安装

本文基于的Linux是Ubuntu 12.04.2 LTS 64bit的系统，安装R语言软件包可以通过apt-get实现。

在Linux Ubuntu中安装R语言

```{bash}

#检查R是否已安装
~  R
The program 'R' is currently not installed.  You can install it by typing:
sudo apt-get install r-base-core

# 根据提示安装R语言软件包
~ sudo apt-get install r-base-core

# 检查R的版本
~ R --version
R version 2.14.1 (2011-12-22)
Copyright (C) 2011 The R Foundation for Statistical Computing
ISBN 3-900051-07-0
Platform: x86_64-pc-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under the terms of the
GNU General Public License version 2.
For more information about these matters see

http://www.gnu.org/licenses/.

```

发现R安装的默认版本是2.14.1，这与本书的中R的版本是不符的，接下来我们希望安装最新版本R的软件包。

## 3. R在Ubuntu中最新版本安装

首先，删除Linux Ubuntu系统中原有的R软件包。

```{bash}

sudo apt-get autoremove r-base-core
```

接下来，我们找到一个Ubuntu的软件源镜像(http://mirror.bjtu.edu.cn/cran/bin/linux/ubuntu/) ,Linux Ubuntu 12.04对应的名字是precise，进入到precise/目录，找到r-base-core相关的文件，发现有不同的R的版本。

![](http://blog.fens.me/wp-content/uploads/2014/03/source.list_.png)

把这个软件源，增加到apt的sources.list文件中

```{bash}

# 在sources.list文件最下面，新加一行
~ sudo sh -c "echo deb http://mirror.bjtu.edu.cn/cran/bin/linux/ubuntu precise/ >>/etc/apt/sources.list"

#更新源
~ sudo apt-get update

#再次安装R语言软件包
~ sudo apt-get install r-base-core

# 检查R的版本
~ R --version
R version 3.0.3 (2014-03-06) -- "Warm Puppy"
Copyright (C) 2014 The R Foundation for Statistical Computing
Platform: x86_64-pc-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under the terms of the
GNU General Public License versions 2 or 3.
For more information about these matters see

http://www.gnu.org/licenses/.
```

这时我们就安装了，最新的R语言3.0.3版本。

## 4. R在Ubuntu中指定版本安装

由于本书中的例子，大部分是基于3.0.1完成的，RHadoop的例子是基于2.15.3完成的，因此我们还需要指定R的安装版本。

### 安装R的2.15.3版本

```{bash}

# 删除系统中原有的R软件包
~ sudo apt-get autoremove r-base-core

# 安装R的2.15.3版本
~ sudo apt-get install r-base-core=2.15.3-1precise0precise1

# 检查R语言软件包版本
~ R --version
R version 2.15.3 (2013-03-01) -- "Security Blanket"
Copyright (C) 2013 The R Foundation for Statistical Computing
ISBN 3-900051-07-0
Platform: x86_64-pc-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under the terms of the
GNU General Public License versions 2 or 3.
For more information about these matters see

http://www.gnu.org/licenses/.

```

### 安装R的3.0.1版本

```{bash}

# 删除系统中原有的R软件包
~ sudo apt-get autoremove r-base-core

# 安装R的3.0.1版本
~ sudo apt-get install r-base-core=3.0.1-6precise0

# 检查R语言软件包版本
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

这样我们就可以很方便的，指定安装不同版本的R的语言。

#### 转载请注明出处：http://blog.fens.me/r-install-ubuntu/




