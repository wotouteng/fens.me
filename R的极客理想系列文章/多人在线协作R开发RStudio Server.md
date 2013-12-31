多人在线协作R开发RStudio Server
========

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处： http://blog.fens.me/r-rstudio-server/

![多人在线协作R开发RStudio Server](http://blog.fens.me/wp-content/uploads/2013/07/rstudio-title.png)

#### 前言

RStudio是R语言开发中的利器，是一个IDE集成环境。RStudio Server版本提供了web的功能，可以安装远程计算机，通过web进行访问，支持多用户。如此神器，快来动手试一下吧。

#### 目录

* 系统环境
* RStudio Server安装
* RStudio Server使用
* RStudio Server多人协作

## 1. 系统环境

操作系统: Linux Ubuntu 64bit 12.04.2 LTS

R语言: 3.0.1

```{bash}
~ uname -a
Linux conan-deskop 3.5.0-23-generic #35~precise1-Ubuntu SMP Fri Jan 25 17:13:26 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux

~ cat /etc/issue
Ubuntu 12.04.2 LTS \n \l
```

RStudio Server官方介绍：http://www.rstudio.com/ide/download/server

安装R语言

```{bash}

~ sudo apt-get install r-base

#R版本 3.0.1
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

## 2. RStudio Server安装

下载并安装

```{bash}
~ sudo apt-get install gdebi-core
~ sudo apt-get install libapparmor1  # Required only for Ubuntu, not Debian
~ wget http://download2.rstudio.org/rstudio-server-0.97.551-amd64.deb
~ sudo gdebi rstudio-server-0.97.551-amd64.deb
```

查看rstudio-server

```{bash}
~ ps -aux|grep rstudio-server
998       2914  0.0  0.1 192884  2568 ?        Ssl  10:40   0:00 /usr/lib/rstudio-server/bin/rserver
```

rstudio-server的服务已启动, 8787端口被打开。

## 3. RStudio Server使用

通过浏览器，我们访问RStudio Server: http://192.168.1.13:8787

![](http://blog.fens.me/wp-content/uploads/2013/07/rstudio-login.png)

通过Linux系统用户登陆: conan:conan111

![](http://blog.fens.me/wp-content/uploads/2013/07/rstudio-web.png)

太帅了是不是，web版的RStudio!!

### 系统配置和系统管理

* 系统配置
* 系统管理

其他操作和单机版的RStudio一样。

## 4. RStudio Server多人协作

### 1). 增加新用户

```{bash}

~ sudo groupadd hadoop 
~ sudo useradd hadoop -g hadoop;
~ sudo passwd hadoop 
~ sudo adduser hadoop sudo
~ sudo mkdir /home/hadoop 
~ sudo chown -R hadoop:hadoop /home/hadoop

#测试账号设置成功
~ ssh hadoop@localhost
~ bash
~ pwd
/home/hadoop
```

新打开浏览器窗口通过 hadoop账号登陆

![](http://blog.fens.me/wp-content/uploads/2013/07/rstudio-web2.png)

### 2). Git代码共享

安装git

```{bash}
sudo apt-get install git

#生成rsa密钥对
ssh-keygen -t rsa

#查看公钥并
cat /home/conan/.ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDMmnFyZe2RHpXaGmENdH9kSyDyVzRas4GtRwMNx+qQ4QsB8xVTrIbFayG2ilt+P8UUkVYO0qtUJIaLRjGy/SvQzzL7JKX12+VyYoKTfKvZZnANJ414d6oZpbDwsC0Z7JARcWsFyTW1KxOMyesmzNNdB+F3bYN9sYNiTkOeVNVYmEQ8aXywn4kcljBhVpT8PbuHl5eadSLt5zpN6bcX7tlquuTlRpLi1e4K+8jQo67H54FuDyrPLUYtVaiTNT/xWN6IU+DQ9CbfykJ0hrfDU1d1LiLQ4K2Fdg+vcKtB7Wxez2wKjsxb4Cb8TLSbXdIKEwSOFooINw25g/Aamv/nVvW1 conan@conan-deskop
```

把公钥并上传到github

创建一个新的Project: rstudio-demo

https://github.com/bsspirit/rstudio-demo

绑定本定项目和rstudio-demo

```{bash}
~ cd /home/conan/R/github
~ git init
~ git add .
~ git commit -m 'first comment'
~ git remote add origin git@github.com:bsspirit/rstudio-demo.git
~ git push -u origin master
```

打开RStudio设置到/home/conan/R/github目录，tools–>version control –> project setup

![](http://blog.fens.me/wp-content/uploads/2013/07/rstudio-git.png)

在RStudio中修改sayHello.r的代码

```{bash}
sayHello<-function(name){
  print(paste("hello",name))
}

sayHello("Conan")
sayHello("World")
```

提交：点击tools–>version control–> commit

![](http://blog.fens.me/wp-content/uploads/2013/07/rstudio-git2.png)

上传到github：点击tools–>version control–> push

![](http://blog.fens.me/wp-content/uploads/2013/07/rstudio-git3.png)

RStudio有如此强大的功能，极大的降低了编程的门槛。还没有用过的同学，赶紧去体验一把吧。极客的感觉！

#### 转载请注明出处：http://blog.fens.me/r-rstudio-server/

