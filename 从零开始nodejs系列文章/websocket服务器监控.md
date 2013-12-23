websocket服务器监控
=============

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-websocket-monitor/

![websocket服务器监控](http://blog.fens.me/wp-content/uploads/2013/08/monitor.png)

#### 前言
Websocket技术的强大之处在于，打通了浏览器和服务器的双向通信的高速通道。有了如此神器，我们就可以在web上面“为所欲为”了。

在web上面，模拟一个Shell窗口，已经不稀奇了。我们再通过shell获得Linux系统(CPU,IO)信息，时时返回到web端，通过Highcharts生成系统监控图，是不是很高端！？

快动起来吧，你也可以做到的。

#### 目录

1. 系统架构设计
2. 通过Shell获得CPU及IO信息
3. 创建nodejs项目
4. 实现websocket服务器端
5. 实现websocket客户端
6. 用Highcharts生成时时系统监控图

## 1. 系统架构设计

![](http://blog.fens.me/wp-content/uploads/2013/08/monitor-architect.png)

### 我的系统环境：

+ Linux: Ubuntu 12.04.2 LTS 64bit deskop
+ Nodejs: npm 1.2.21, node v0.11.2

```{bash}

~ uname -a
Linux conan-deskop 3.5.0-23-generic #35~precise1-Ubuntu SMP Fri Jan 25 17:13:26 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux
~ cat /etc/issue
Ubuntu 12.04.2 LTS \n \l

~ npm -v
1.2.21
~ node -v
v0.11.2
```

## 2. 通过Shell获得CPU及IO信息

### 安装软件包sysstat

```{bash}
sudo apt-get install sysstat
```

### 配置sysstat

```{bash}
~ sudo vi /etc/default/sysstat
ENABLED="true"

~ sudo /etc/init.d/sysstat restart
```

### 查看CPU的使用情况

```{bash}

~ sar  1
Linux 3.5.0-23-generic (conan-deskop)   2013年08月23日  _x86_64_        (2 CPU)
18时13分39秒     CPU     %user     %nice   %system   %iowait    %steal     %idle
18时13分40秒     all      0.52      0.00      0.52      0.00      0.00     98.96
18时13分41秒     all      0.52      0.00      0.52      0.00      0.00     98.96
18时13分42秒     all      0.00      0.00      0.52      0.00      0.00     99.48
18时13分43秒     all      0.00      0.00      1.03      0.00      0.00     98.97
18时13分44秒     all      0.00      0.00      0.52      0.00      0.00     99.48
```

我们只关系idle的情况。公式：CPU时间 = 100 – idle

### 查看内存的情况

```{bash}
~ sar -r 1
Linux 3.5.0-23-generic (conan-deskop)   2013年08月23日  _x86_64_        (2 CPU)
18时14分30秒 kbmemfree kbmemused  %memused kbbuffers  kbcached  kbcommit   %commit  kbactive   kbinact
18时14分31秒    749672   1301452     63.45    123460    643808   2205152     53.18    716128    440348
18时14分32秒    749672   1301452     63.45    123460    643808   2205152     53.18    716128    440348
18时14分33秒    749672   1301452     63.45    123460    643808   2205152     53.18    716128    440348
18时14分34秒    749672   1301452     63.45    123460    643808   2205152     53.18    716128    440348
```

我们只关系memused的情况。公式：内存用量 = 100 – memused

## 3. 创建nodejs项目

创建一个express3的项目, 还没有装好nodejs环境的同学，请参考：[准备Nodejs开发环境Ubuntu](http://blog.fens.me/nodejs-enviroment)

```{bash}
~ cd /home/conan/nodejs
~ express -e nodejs-alert
~ cd nodejs-alert
~ sudo npm install
```

实现websocket通信，我们还需要安装socket.io

```{bash}
~ sudo npm install socket.io
```

## 4. 实现websocket服务器端

修改app.js文件，webscoket的实现用到socket.io，读取系统进程用到child_process。

```{bash}

var express = require('express')
  , path = require('path')
  , app = express()
  , server = require('http').createServer(app)
  , io = require('socket.io').listen(server)
  , worker = require('child_process');

// 其他代码省略
....

```

## 5. 实现websocket客户端

### 1). 首先是view/alert.html

```{bash}

~ vi view/alert.html

<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Socket.io - System Alert</title>
<script src="javascripts/jquery.min.js"></script>
<script src="/socket.io/socket.io.js"></script>
<script src="javascripts/sysAlert.js"></script>
</head>
<body>
<h1>Socket.io - System Alert</h1>
<div id="container" style="min-width: 310px; height: 400px; margin: 0 auto"></div>
<script src="javascripts/highcharts/highcharts.js"></script>
<script src="javascripts/highcharts/modules/exporting.js"></script>
</body>
</html>
```

### 2). 下载必须的jquery.min.js和highcharts的整个库

### 3). 增加 javascripts/sysAlert.js 文件，用于客户端的实现。

+ 调用socket.io实现和服务器的通信
+ 调用Highcharts生成动态图

```{bash}

    // highcharts部分代码
    Highcharts.setOptions({
        global: {
            useUTC: false
        }
    });

    $('#container').highcharts({
        chart: {
            type: 'spline',
            animation: Highcharts.svg,
            marginRight: 10
        },
    
    // 代码省略
    ....
    });

    //// socket.io部分代码
    var socket = io.connect('http://192.168.1.20');
    socket.on('system', function (data) {

    // 代码省略
    ...
    });
```

下面是生成的 系统监控动态图！

## 6. 用Highcharts生成时时系统监控图

![](http://blog.fens.me/wp-content/uploads/2013/08/alert.png)

内存，占用情况大概为84%，保持稳定。
CPU，占用情况。每次CPU有变化时，我都在做矩阵计算。

### R语言矩阵计算代码

```{bash}
a<-matrix(1:1000000,ncol=1000)->b
b %*% a
```

#### 转载请注明出处：http://blog.fens.me/nodejs-websocket-monitor/

