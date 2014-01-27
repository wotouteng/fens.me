Nodejs异步异常处理domain
=============

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-core-domain/ ‎

![Nodejs异步异常处理domain](http://blog.fens.me/wp-content/uploads/2014/01/nodejs-domain.png)

#### 前言

程序开发中，最麻烦的事情之一就是异常处理；对于Nodejs程序开发，最麻烦的事情莫过于异步异常处理。

以MVC的多层架构设计角度，异常总是要一层一层向上抛出，最后在客户端出打印错误。但是，Nodejs都是异步异常，try..catch根本就捕捉不到，就会给我们的程序设计带来不小的麻烦，经常会有未处理的runtime异常，让整个系统挂掉。

#### 目录

1. Nodejs异常处理
2. Nodejs异步异常处理
3. domain介绍
4. domain的API介绍
5. domain异步异常特例

## 1. Nodejs同步异常处理

### 系统环境

* win7 64bit
* Nodejs:v0.10.5
* Npm:1.2.19

创建项目

```{bash}

~ D:\workspace\javascript>mkdir nodejs-domain && cd nodejs-domain
```

新建文件：sync.js，模拟同步异常的处理

```{bash}
~ vi sync.js

function sync_error() {
    var r = Math.random() * 10;
    console.log("random num is " + r);
    if (r > 5) {
        throw new Error("Error: random num" + r + " > 5");
    }
}

setInterval(function () {
    try {
        sync_error();
    } catch (err) {
        console.log(err);
    }

}, 1000)
```

运行程序

```{bash}
~ D:\workspace\javascript\nodejs-domain>node sync.js
random num is 1.067440479528159
random num is 6.284254263155162
[Error: Error: random num6.284254263155162 > 5]
random num is 8.445568478200585
[Error: Error: random num8.445568478200585 > 5]
random num is 2.79862868366763
random num is 5.452311446424574
[Error: Error: random num5.452311446424574 > 5]
random num is 3.725348354782909
random num is 7.590636070817709
[Error: Error: random num7.590636070817709 > 5]
random num is 9.584896392188966
[Error: Error: random num9.584896392188966 > 5]
random num is 3.63708304008469
random num is 5.747077965643257
[Error: Error: random num5.747077965643257 > 5]
random num is 1.0771577199921012
random num is 8.898805833887309
[Error: Error: random num8.898805833887309 > 5]
random num is 6.59792885184288
[Error: Error: random num6.59792885184288 > 5]
random num is 1.8532328261062503
random num is 3.6028534593060613
random num is 2.7523675211705267
random num is 1.598257850855589
```

通过try..catch可以很好的抓到同步程序的异常。

## 2. Nodejs异步异常处理

新建文件：async.js，模拟异步异常处理

```{bash}
~ vi async.js

function async_error() {
    setTimeout(function(){
        var r = Math.random() * 10;
        console.log("random num is " + r);
        if (r > 5) {
            throw new Error("Error: random num" + r + " > 5");
        }
    },10)

}

setInterval(function () {
    try {
        async_error();
    } catch (err) {
        console.log(err);
    }
}, 1000)
```

运行程序

```{bash}
~ D:\workspace\javascript\nodejs-domain\sync.js:5
        throw new Error("Error: random num" + r + " > 5");
              ^
Error: Error: random num9.974474618211389 > 5
    at trycatch (D:\workspace\javascript\nodejs-domain\sync.js:5:15)
    at Timer. (D:\workspace\javascript\nodejs-domain\sync.js:10:5)
    at Timer.timer.ontimeout (timers.js:247:14)
```

try..catch，无法捕捉异步异常！

修改async.js, 通过process.on()打印错误信息。

```{bash}

~ vi async.js

function async_error() {
    setTimeout(function(){
        var r = Math.random() * 10;
        console.log("random num is " + r);
        if (r > 5) {
            throw new Error("Error: random num" + r + " > 5");
        }
    },10)

}

setInterval(function () {
    try {
        async_error();
    } catch (err) {
        console.log(err);
    }
}, 1000)

process.on('uncaughtException', function (err) {
    console.log(err);
});
```

运行程序

```{bash}
~ D:\workspace\javascript\nodejs-domain>node async.js
random num is 9.33843155624345
[Error: Error: random num9.33843155624345 > 5]
random num is 7.894433259498328
[Error: Error: random num7.894433259498328 > 5]
random num is 2.532815719023347
random num is 6.0961083066649735
[Error: Error: random num6.0961083066649735 > 5]
random num is 5.138748907484114
[Error: Error: random num5.138748907484114 > 5]
```

通过process.on(‘uncaughtException’)的内置函数，虽然我们可以记录下这个错误的日志，而且进程也不会异常退出，但是我们是没有办法对发现错误的请求友好返回的，只能够让它超时返回。

## 3. domain介绍

node在v0.8+版本的时候，发布了一个模块domain。这个模块做的就是try catch所无法做到的：捕捉异步回调中出现的异常。

domain模块，把处理多个不同的IO的操作作为一个组。注册事件和回调到domain，当发生一个错误事件或抛出一个错误时，domain对象会被通知，不会丢失上下文环境，也不导致程序错误立即推出，与process.on(‘uncaughtException’)不同。

domain的发布页http://nodejs.org/api/domain.html

用domain捕捉异常，新建文件domain.js

```{bash}

~ vi domain.js

var domain = require('domain');

function sync_error() {
    var r = Math.random() * 10;
    console.log("sync num is " + r);
    if (r > 5) {
        throw new Error("sync: random num" + r + " > 5");
    }
}

function async_error() {
    setTimeout(function(){
        var r = Math.random() * 10;
        console.log("async num is " + r);
        if (r > 5) {
            throw new Error("async: random num" + r + " > 5");
        }
    },10)

}

var d = domain.create();
d.on('error',function(err){
    console.log(err);
});

setInterval(function () {
    d.run(sync_error);
    d.run(async_error);
}, 1000)
```

运行程序

```{bash}

~ D:\workspace\javascript\nodejs-domain>node domain.js
sync num is 8.492766928393394
{ [Error: sync: random num8.492766928393394 > 5]
  domain:
   { domain: null,
     _events: { error: [Function] },
     _maxListeners: 10,
     members: [] },
  domainThrown: true }
sync num is 4.991524459328502
async num is 7.5735537661239505
{ [Error: async: random num7.5735537661239505 > 5]
  domain:
   { domain: null,
     _events: { error: [Function] },
     _maxListeners: 10,
     members: [] },
  domainThrown: true }
sync num is 4.626072463579476
async num is 9.48660139227286
{ [Error: async: random num9.48660139227286 > 5]
  domain:
   { domain: null,
     _events: { error: [Function] },
     _maxListeners: 10,
     members: [] },
  domainThrown: true }
sync num is 2.3057156521826982
async num is 4.5645097037777305
sync num is 2.0251641585491598
async num is 7.712894310243428
{ [Error: async: random num7.712894310243428 > 5]
  domain:
   { domain: null,
     _events: { error: [Function] },
     _maxListeners: 10,
     members: [] },
  domainThrown: true }
```

我们发现domain，可以同时捕捉到同步异常(sync)和异步异常(async)。

## 4. domain的API介绍

### 基本概念

* 隐式绑定: 把在domain上下文中定义的变量，自动绑定到domain对象
* 显式绑定: 把不是在domain上下文中定义的变量，以代码的方式绑定到domain对象

### API介绍

* domain.create(): 返回一个domain对象
* domain.run(fn): 在domain上下文中执行一个函数，并隐式绑定所有事件，定时器和低级的请求。
* domain.members: 已加入domain对象的域定时器和事件发射器的数组。
* domain.add(emitter): 显式的增加事件
* domain.remove(emitter): 删除事件
* domain.bind(callback): 以return为封装callback函数
* domain.intercept(callback): 同domain.bind，但只返回第一个参数
* domain.enter(): 进入一个异步调用的上下文，绑定到domain
* domain.exit(): 退出当前的domain，切换到不同的链的异步调用的上下文中。对应domain.enter()
* domain.dispose(): 释放一个domain对象，让node进程回收这部分资源

## 5. domain异步异常特例

下面这个例子，domain将捕捉不到异步异常

```{bash}

var domain = require('domain');
var EventEmitter = require('events').EventEmitter;

var e = new EventEmitter();

var timer = setTimeout(function () {
    e.emit('data');
}, 10);

function next() {
    e.once('data', function () {
        throw new Error('Receive data error!');
    });
}

var d = domain.create();
d.on('error', function (err) {
    console.log(err);
});

d.run(next);
```

运行程序

```{bash}
~ D:\workspace\javascript\nodejs-domain\special.js:12
        throw new Error('Receive data error!');
              ^
Error: Receive data error!
    at EventEmitter. (D:\workspace\javascript\nodejs-domain\special.js:12:15)
    at EventEmitter.g (events.js:175:14)
    at EventEmitter.emit (events.js:92:17)
    at null._onTimeout (D:\workspace\javascript\nodejs-domain\special.js:7:7)
    at Timer.listOnTimeout [as ontimeout] (timers.js:110:15)
```

如果timer和e两个关键的对象在初始化的时候都时没有在domain的范围之内，因此，当在next函数中监听的事件被触发，执行抛出异常的回调函数时，其实根本就没有处于domain的包裹中，就不会被domain捕获到异常了！

修改程序代码

```{bash}

var domain = require('domain');
var EventEmitter = require('events').EventEmitter;

var e = new EventEmitter();

var timer = setTimeout(function () {
    e.emit('data');
}, 10);

function next() {
    e.once('data', function () {
        throw new Error('Receive data error!');
    });
}

var d = domain.create();
d.on('error', function (err) {
    console.log(err);
});

d.add(e);
d.add(timer);

d.run(next);
```

增加e和timer到domain的范围内，运行程序

```{bash}
~ D:\workspace\javascript\nodejs-domain>node special.js
{ [Error: Receive data error!]
  domain:
   { domain: null,
     _events: { error: [Function] },
     _maxListeners: 10,
     members: [ [Object], [Object] ] },
  domainThrown: true }
```

domain特例代码摘自：http://cnodejs.org/topic/516b64596d38277306407936

通过domain模块，我们就可以好好设计Nodejs系统的异常管理了。

#### 转载请注明出处：http://blog.fens.me/nodejs-core-domain/ ‎







































