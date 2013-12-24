Async多任务时间管理
===========

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-async-timer/

![Async多任务时间管理](http://blog.fens.me/wp-content/uploads/2013/11/nodejs-async-timer.png)

#### 前言

做服务器端开发时，经常会遇到时间管理的功能需求，比如每2秒刷新一次，每三分钟做一次统计计算，周一至周五9点30启动一个定时任务等等。

很多时候我们会把这些定时任务，交给linux系统的Crontab来实现。不过，有时为了增加系统的灵活性，我们需要在服务器后台实现。

对于单线程的Nodejs，如何控制多任务的时间管理呢？

#### 目录

1. 需求描述
2. Nodejs的实现方案setInterval
3. Async多任务时间管理

## 1. 需求描述

基于Nodejs的express3构建的web框架，需要在周一至周五，早上9点15分时，分别启动程序A和程序B，程序C。下午16点程序A,B,C停止。

* 程序A: 每1秒去redis取数据一次，保留在Nodejs的全局变量G中。
* 程序B: 每10秒去mysql取数据一次，通过websocket直接访问给客户端。
* 程序C: 每5秒对全局变量G，进行平均值计算，然后通过websocket直接访问给客户端。

## 2. Nodejs的实现方案setInterval

### 系统环境：

* win7 64bit
* Nodejs:v0.10.5
* Npm:1.2.19

出初化项目：

```{bash}
~ cd D:\workspace\javascript\nodejs-async\demo
~ express -e timers
~ cd timers && npm install
~ npm install moment
~ npm install twix
```

编辑文件：app.js，在文件最面下增加新代码

```{bash}
...

//moment,twix时间工具
var  moment = require('moment')
    ,twix = require('twix');

//判断程序启动时间
function isTime(){
    var hms = 'HHmmss';
    return moment("091500",hms).twix(moment("160000",hms)).contains(moment());
}

//打印日志
if(isTime()){
    console.log("===============Working time===================");
}

//日志时间格式化
function now() {
    return moment().format("HH:mm:ss");
}

//全局变量G
var G = 0;

//模拟程序A
function A() {
    console.log(now() + " A(s1)=> {G:" + (G++) + "} Cache G");
}

//模拟程序B
function B() {
    console.log(now() + " B(s10)=> {B:10} TO client");
}

//模拟程序C
function C() {
    console.log(now() + " C(s5)=> {G:" + (G / 5) + "} TO client");
    G = 0;
}

//分别对A,B,C程序进行时间管理
setInterval(function () {
    if(isTime()){
        A()
    };
}, 1000);

setInterval(function () {
    if(isTime()){
        C();
    }
}, 5 * 1000);

setInterval(function () {
    if(isTime()) {
        B();
    }
}, 10 * 1000);
```

运行nodejs，查看日志输出

```{bash}
D:\workspace\javascript\nodejs-async\demo\timers>node app.js
===============Working time===================
Express server listening on port 3000
15:02:24 A(s1)=> {G:0} Cache G
15:02:25 A(s1)=> {G:1} Cache G
15:02:26 A(s1)=> {G:2} Cache G
15:02:27 A(s1)=> {G:3} Cache G
15:02:28 A(s1)=> {G:4} Cache G
15:02:28 C(s5)=> {G:1} TO client
15:02:29 A(s1)=> {G:0} Cache G
15:02:30 A(s1)=> {G:1} Cache G
15:02:31 A(s1)=> {G:2} Cache G
15:02:32 A(s1)=> {G:3} Cache G
15:02:33 A(s1)=> {G:4} Cache G
15:02:33 C(s5)=> {G:1} TO client
15:02:33 B(s10)=> {B:10} TO client
15:02:34 A(s1)=> {G:0} Cache G
15:02:35 A(s1)=> {G:1} Cache G
15:02:36 A(s1)=> {G:2} Cache G
15:02:37 A(s1)=> {G:3} Cache G
15:02:38 A(s1)=> {G:4} Cache G
15:02:38 C(s5)=> {G:1} TO client
15:02:39 A(s1)=> {G:0} Cache G
15:02:40 A(s1)=> {G:1} Cache G
15:02:41 A(s1)=> {G:2} Cache G
15:02:42 A(s1)=> {G:3} Cache G
```

* 程序A,每1秒运行一次，给G+1。
* 程序B,每10秒运行一次，输出到客户端。
* 程序C,每5秒运行一次，取G的平均值，给G赋值为0，输出到客户端。

虽然完成了功能需求，但是代码不美观！如果再增加任务D,E,F….代码不好维护。

## 3. Async多任务时间管理

下面我们用async包，对上面的浏览进行封装。

Async包函数的详细介绍，请参考文章：[Nodejs异步流程控制Async](http://blog.fens.me/nodejs-async/)

安装async依赖

```{bash}
~ npm install async
```

新建一个文件：async-app.js，

```{bash}
~ cp app.js async-app.js
```

也就是重新复制一份app.js, 把setInterval的函数都去掉。

增加async的时间管理代码

```{bash}

var async = require('async');

var arr = [
    {fun: A, delay: 1000, test: isTime},
    {fun: B, delay: 10 * 1000, test: isTime},
    {fun: C, delay: 5 * 1000, test: isTime}
];

async.each(arr, function (item, callback) {
    async.whilst(item.test,function(cb) {
            item.fun();
            setTimeout(cb, item.delay);
        },function(err) {
            console.log("Not working time!");
        }
    );
}, function (err) {
    log('Error: ' + err);
});
```

运行nodejs，查看日志输出

```{bash}
D:\workspace\javascript\nodejs-async\demo\timers>node async-app.js
===============Working time===================
15:13:57 A(s1)=> {G:0} Cache G
15:13:57 B(s10)=> {B:10} TO client
15:13:57 C(s5)=> {G:0.2} TO client
Express server listening on port 3000
15:13:58 A(s1)=> {G:0} Cache G
15:13:59 A(s1)=> {G:1} Cache G
15:14:00 A(s1)=> {G:2} Cache G
15:14:01 A(s1)=> {G:3} Cache G
15:14:02 C(s5)=> {G:0.8} TO client
15:14:02 A(s1)=> {G:0} Cache G
15:14:03 A(s1)=> {G:1} Cache G
15:14:04 A(s1)=> {G:2} Cache G
15:14:05 A(s1)=> {G:3} Cache G
15:14:06 A(s1)=> {G:4} Cache G
15:14:07 B(s10)=> {B:10} TO client
15:14:07 C(s5)=> {G:1} TO client
15:14:07 A(s1)=> {G:0} Cache G
15:14:08 A(s1)=> {G:1} Cache G
15:14:09 A(s1)=> {G:2} Cache G
15:14:10 A(s1)=> {G:3} Cache G
15:14:11 A(s1)=> {G:4} Cache G
15:14:12 C(s5)=> {G:1} TO client
15:14:12 A(s1)=> {G:0} Cache G
```

* 构建一个arr数组，封装调用的A,B,C的参数。
* 使用async.each函数，对arr的item异步并行
* 使用async.whilst函数，对任务启动时间进行判断，并根据delay运行任务

程序运行结果与setInterval一样。而代码更利于维护，一旦需要增减任务，简单地修改arr的数组就行了，其他的代码都不用动！！

很难想象，其他的语言能像js一样，有如此之重构的能力。越来越感到惊喜！

#### 转载请注明出处：http://blog.fens.me/nodejs-async-timer/

