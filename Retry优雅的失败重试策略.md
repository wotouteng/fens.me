Retry优雅的失败重试策略
========

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-retry

![Retry优雅的失败重试策略](http://blog.fens.me/wp-content/uploads/2013/12/nodejs-retry.png)

#### 前言

异常，在程序设计中是至关重要的，但经常会被设计的过于复杂！

有时候，我们调用一个网络访问的函数，各种原因造成访问失败，会获得一个异常。这个异常可能是请求超时，也许重连一次就好了。代码中，我们会用到try..catch..finally，当应用中异常的规则不断增加，单纯的try..catch控制的重试就显得不够用了。

retry包，可以非常简单地帮助我们封装需要“重试”的函数。

#### 目录

1. retry介绍
2. retry安装
3. retry的API介绍
4. retry使用

## 1. retry介绍

retry是一个nodejs库，帮助用户可以自定义失败的操作策略。

retry源代码：https://github.com/tim-kos/node-retry

## 2. retry安装

### 系统环境

* win7 64bit
* Nodejs:v0.10.5
* Npm:1.2.19

### 安装retry

```{bash}

~ D:\workspace\javascript>mkdir nodejs-retry && cd nodejs-retry

~ D:\workspace\javascript\nodejs-retry>npm install retry
npm http GET https://registry.npmjs.org/retry
npm http 304 https://registry.npmjs.org/retry
retry@0.6.0 node_modules\retry
```

## 3. retry的API介绍

### options配置

* retries:最大重试次数，默认10
* factor:指数因子使用，默认2
* minTimeout: 第一次重试前等待时间，默认1000ms
* maxTimeout: 间隔两次重试的等待时间，默认Infinity
* randomize: 随机化超时时间，默认false

### 超时计算公式

```{bash}
var Math.min(random * minTimeout * Math.pow(factor, attempt), maxTimeout);
```

* retry.operation([options]): 创建一个RetryOperation对象
* retry.timeouts([options]): 返回一个超时列表，所有时间都是毫秒
* new RetryOperation(timeouts): 创建RetryOperation对象
* retryOperation.errors(): 返回被retryOperation.retry()处理的，所有错误列表
* retryOperation.mainError(): 返回一个越多发生的错误对象
* retryOperation.attempt(fn, timeoutOps): 封装一个功能函数到retry，当出错后，会重试功能函数
* retryOperation.retry(error): 判断是否需要重试。error=true，要重试，error=false，不要重试
* retryOperation.attempts(): 返回已重试次数

## 4. retry使用

我们构造一个简单的网络爬虫，分别爬取baidu首页和facebook首页。由于在中国facebook被禁止访问，因此我们的爬虫是连接不到facebook的，会一直报错。retry会监听到这个错误，并自动地实现重试的操作。

## 新建文件app.js

```{bash}

~ vi app.js

var retry = require('retry')
    , http = require('http');

function get(url, cb) {
    var options = {
        hostname: url,
        port: 80,
        method: 'GET'
    };

    var req = http.request(options, function (res) {
        console.log('STATUS: ' + res.statusCode);
        console.log('HEADERS: ' + JSON.stringify(res.headers));
        res.setEncoding('utf8');
        res.on('data', function (chunk) {
//            console.log('BODY: ' + chunk);
        });
    });

    req.on('error', function (e) {
        console.log('problem with request: ' + e.message);
        cb(e.message);
    });

    req.end();

}

function retryGet(url, cb) {
    var operation = retry.operation();
    operation.attempt(function (currentAttempt) {
        console.log("Connect Times:" + currentAttempt + ":" + url);
        get(url, function (err) {
            if (operation.retry(err)) {
                return;
            }
            cb(err ? operation.mainError() : null);
        });
    });
}
var baidu = "www.baidu.com";
retryGet(baidu);

var facebook = "www.facebook.com";
retryGet(facebook);
```

### 启动程序

```{bash}
~ D:\workspace\javascript\nodejs-retry>node app.js
Connect Times:1:www.baidu.com
Connect Times:1:www.facebook.com
STATUS: 200
HEADERS: {"date":"Mon, 16 Dec 2013 02:50:38 GMT","content-type":"text/html","transfer-encoding":"chunked","connection":"
Keep-Alive","vary":"Accept-Encoding","set-cookie":["BAIDUID=C2AC6E4F1F722771064C91DB1D7FAF74:FG=1; expires=Thu, 31-Dec-3
7 23:55:55 GMT; max-age=2147483647; path=/; domain=.baidu.com","BDSVRTM=0; path=/","H_PS_PSSID=4534_1455_4321_4414_4262_
4451; path=/; domain=.baidu.com"],"p3p":"CP=\" OTI DSP COR IVA OUR IND COM \"","expires":"Mon, 16 Dec 2013 02:50:20 GMT"
,"cache-control":"private","server":"BWS/1.1","bdpagetype":"1","bdqid":"0xc5ae42689eea7b73","bduserid":"0"}
problem with request: connect ETIMEDOUT
Connect Times:2:www.facebook.com
problem with request: connect ETIMEDOUT
Connect Times:3:www.facebook.com
problem with request: connect ETIMEDOUT
Connect Times:4:www.facebook.com
problem with request: connect ETIMEDOUT
Connect Times:5:www.facebook.com
problem with request: connect ETIMEDOUT
Connect Times:6:www.facebook.com
problem with request: connect ETIMEDOUT
Connect Times:7:www.facebook.com
problem with request: connect ETIMEDOUT
Connect Times:8:www.facebook.com
```

我们看到程序运行的情况，确实如我们所预料的。抓取百度首页，第一次就成功了，只运行了一次。而抓取facebook，由于一直不成功，所以会反复地重试。

retry会极大地帮助了我们处理这种不稳定连接的操作，定义自己的失败重试策略。

#### 转载请注明出处：http://blog.fens.me/nodejs-retry

