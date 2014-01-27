restify构建REST服务
=========

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-restify/

![restify构建REST服务](http://blog.fens.me/wp-content/uploads/2014/01/nodejs-restify.png)

#### 前言

随着互联网应用的兴起，web2.0时代的到来，越来越多的人，选择用REST编程来代替原来的页面渲染。REST以资源为中心的web服务，分离了展现层和服务层，让前端和后端程序员能更专注于自己擅长的领域。

restify让REST变得如此简单！

#### 目录

1. 什么是REST?
2. restify介绍
3. restify安装
4. restify服务端API
5. restify客户端API

## 1. 什么是REST?

REST(Representational State Transfer表述性状态转移)是一种针对网络应用的设计和开发方式，可以降低开发的复杂性，提高系统的可伸缩性。

REST 定义了一组体系架构原则，您可以根据这些原则设计以系统资源为中心的 Web 服务，包括使用不同语言编写的客户端如何通过 HTTP 处理和传输资源状态。 如果考虑使用它的 Web 服务的数量，REST 近年来已经成为最主要的 Web 服务设计模式。 事实上，REST 对 Web 的影响非常大，由于其使用相当方便，已经普遍地取代了基于 SOAP 和 WSDL 的接口设计。

REST中的资源所指的不是数据，而是数据和表现形式的组合，比如“最新访问的10位会员”和“最活跃的10位会员”在数据上可能有重叠或者完全相同，而由于他们的表现形式不同，所以被归为不同的资源，这也就是为什么REST的全名是Representational State Transfer的原因。资源标识符就是URI(Uniform Resource Identifier)，不管是图片，Word还是视频文件，甚至只是一种虚拟的服务，也不管你是xml格式、txt文件格式还是其它文件格式，全部通过 URI对资源进行唯一的标识。

>文字介绍，摘自: http://baike.baidu.com/view/1077487.htm

## 2. restify介绍

restify是一个基于Nodejs的REST应用框架，支持服务器端和客户端。restify比起express更专注于REST服务，去掉了express中的template, render等功能，同时强化了REST协议使用，版本化支持，HTTP的异常处理。

restify提供了DTrace功能，为程序调式带来新的便利！

restifty的发布页：http://mcavage.me/node-restify/

## 3. restify安装

### 系统环境

* Linux: Ubuntu 12.04 LTS 64bit
* node: v0.6.12
* npm: 1.1.4

### 创建项目

```{bash}

~ cd /home/conan/nodejs
~ mkdir nodejs-restify && cd nodejs-restify

~ sudo npm install restify
restify@2.6.1 node_modules/restify
├── assert-plus@0.1.4
├── once@1.3.0
├── deep-equal@0.0.0
├── escape-regexp-component@1.0.2
├── qs@0.6.5
├── tunnel-agent@0.3.0
├── keep-alive-agent@0.0.1
├── lru-cache@2.3.1
├── node-uuid@1.4.0
├── negotiator@0.3.0
├── mime@1.2.11
├── semver@2.2.1
├── spdy@1.14.12
├── backoff@2.3.0
├── formidable@1.0.14
├── csv@0.3.6
├── dtrace-provider@0.2.8
├── verror@1.3.6 (extsprintf@1.0.2)
├── bunyan@0.22.0 (mv@0.0.5)
└── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11,ctype@0.5.2)
```

创建一个简单的rest服务

新建文件：app.js

```{bash}
~ vi app.js

var restify = require('restify');

function respond(req, res, next) {
  res.send('hello ' + req.params.name);
}

var server = restify.createServer();
server.get('/hello/:name', respond);
server.head('/hello/:name', respond);

server.listen(3900, function() {
  console.log('%s listening at %s', server.name, server.url);
});
```

运行程序

```{bash}
~ node app.js
restify listening at http://0.0.0.0:3900
```

使用curl访问测试

```{bash}
#空路径
~ curl localhost:3900
{"code":"ResourceNotFound","message":"/ does not exist"

#正常GET请求
~ curl localhost:3900/hello/conan
"hello conan"

#设置content-type, 返回包括header信息
~ curl -i localhost:3900/hello/conan -H 'accept:text/plain'
HTTP/1.1 200 OK
Content-Type: text/plain
Content-Length: 11
Date: Mon, 13 Jan 2014 08:31:30 GMT
Connection: keep-alive

hello conan

#设置connection:close, 返回包括header信息
~  curl -i localhost:3900/hello/conan -H 'connection:close'
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 13
Date: Mon, 13 Jan 2014 08:29:38 GMT
Connection: close

hello conan
```

我们看到用restify创建REST服务非常的容易，接下来我们将深入学习restify的API。

## 4. restify服务端API

* restify.createServer(): 创建服务器
* server.use(): 注册handler
* server.get(): 路由控制
* server.formatters: 设置content-type
* 异常处理
* socket.IO集成/li>
* Server API
* 组件管理
* Request API
* Response API

### 1). restify.createServer(): 创建服务器

创建服务器代码

```{bash}
var restify = require('restify');

var server = restify.createServer({
  certificate: ...,
  key: ...,
  name: 'MyApp',
});

server.listen(8080);
```

服务器创建参数

* certificate: string, HTTPS服务器的证书
* key: string, HTTPS服务器的证书key
* formatters: object, 自定义的response的content-type
* log: object, 服务器日志，可以配合bunyan一起使用
* name: string, 服务器的response header
* spdy: Object, 允许集成node-spdy服务器
* version: string, 路由版本
* responseTimeHeader: string, X-Response-Time
* responseTimeFormatter: function, 格式化header的值

### 2). server.use(): 注册handler

注册服务器控制组件，按照代码顺序执行，需要放在路由代码之前。

### 3). server.get(): 路由控制

REST响应G请求：

* server.get(): 响应GET请求
* server.post(): 响应POST请求
* server.put(): 响应PUT请求
* server.head(): 响应HEAD请求
* server.del(): 响应DELETE请求

路由控制代码

```{bash}

 function send(req, res, next) {
   res.send('hello ' + req.params.name);
   return next();
 }

 server.post('/hello', function create(req, res, next) {
   res.send(201, Math.random().toString(36).substr(3, 8));
   return next();
 });
 server.put('/hello', send);
 server.get(/^\/([a-zA-Z0-9_\.~-]+)\/(.*)/, send);
 server.head('/hello/:name', send);
 server.del('hello/:name', function rm(req, res, next) {
   res.send(204);
   return next();
 });
```

### 4). server.formatters: 设置Response的content-type

content-type类型

* application/json
* text/plain
* application/octet-stream

增加自定义的content-type:application/foo

```{bash}
var server = restify.createServer({
  formatters: {
    'application/foo': function formatFoo(req, res, body) {
      if (body instanceof Error)
        return body.stack;

      if (Buffer.isBuffer(body))
        return body.toString('base64');

      return util.inspect(body);
    }
  }
});
```

显式的设置Response的content-type

```{bash}
res.setHeader('content-type', 'application/foo');
res.send({hello: 'world'});
```

### 5). 异常处理

异常处理代码

```{bash}
var server = restify.createServer();
server.get('/hello/:name', function(req, res, next) {
  return next(new restify.InvalidArgumentError("I just don't like you"));
});
```

运行程序

```{bash}
~ curl -i localhost:3900/err/conan

HTTP/1.1 409 Conflict
Content-Type: application/json
Content-Length: 60
Date: Mon, 13 Jan 2014 11:06:26 GMT
Connection: keep-alive
```

RestError的内置的异常类型：

* BadDigestError: 400
* BadMethodError: 405
* InternalError: 500
* InvalidArgumentError: 409
* InvalidContentError: 400
* InvalidCredentialsError: 401
* InvalidHeaderError: 400
* InvalidVersionError: 400
* MissingParameterError: 409
* NotAuthorizedError: 403
* RequestExpiredError: 400
* RequestThrottledError: 429
* ResourceNotFoundError: 404
* WrongAcceptError: 406

自定义异常:MyError, errorCode:418

```{bash}

var restify = require('restify');
var util = require('util');

function MyError(message) {
  restify.RestError.call(this, {
    restCode: 'MyError'
    statusCode: 418,
    message: message,
    constructorOpt: MyError
  });
  this.name = 'MyError';
};
util.inherits(MyError, restify.RestError);
```

### 6). Socket.IO集成

服务器端集成代码

```{bash}

var server = restify.createServer();
var io = socketio.listen(server);

server.get('/', function indexHTML(req, res, next) {
    fs.readFile(__dirname + '/index.html', function (err, data) {
        if (err) {
            next(err);
            return;
        }

        res.setHeader('Content-Type', 'text/html');
        res.writeHead(200);
        res.end(data);
        next();
});


io.sockets.on('connection', function (socket) {
    socket.emit('news', { hello: 'world' });
    socket.on('my other event', function (data) {
            console.log(data);
    });
});

server.listen(8080, function () {
    console.log('socket.io server listening at %s', server.url);
});
```

### 7). Server API

Events事件监听：

* NotFound: 404 handler
* MethodNotAllowed: 405 handler
* VersionNotAllowed: 400 handler
* UnsupportedMediaType: 415 handler
* after: 在所有handler之后执行
* uncaughtException: 未处理的异常

注: uncaughtException的处理，可以参考文章, [Nodejs异步异常处理domain](http://blog.fens.me/nodejs-core-domain/%20%E2%80%8E)

Properties配置属性：

* name: string, 服务器名字
* version: string, 路由默认版本
* log: Object, 日志对象
* acceptable: Array(String), content-types列表
* url: string, 服务器信息

Methods函数：

* address(): 绑定地址
* listen(port, [host], [callback]): 启动服务器
* close(): 停止服务器
* pre(): 在路由之前触发的组件
* use(): 注册组件

### 8). 组件管理

restify已支持的组件

* Accept header parsing: 解析aceept header，返回客户端
* Authorization header parsing: HTTP Basic Auth认证
* Date header parsing: 数据头解析
* JSONP support: JSONP请求
* Gzip Response: 设置accept-encoding:gzip
* Query string parsing: 解析URL参数
* Body parsing (JSON/URL-encoded/multipart form): 解析内容
* Static file serving: 静态文件处理
* Throttling: 优化服务器性能配置
* Conditional request handling: 设置请求条件
* Audit logger: 日志记录

注册组件server.use()

```{bash}

var server = restify.createServer();
server.use(restify.acceptParser(server.acceptable));
server.use(restify.authorizationParser());
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.jsonp());
server.use(restify.gzipResponse());
server.use(restify.bodyParser());
server.get(/\/docs\/public\/?.*/, restify.serveStatic({
  directory: './public'
}));
server.use(restify.throttle({
  burst: 100,
  rate: 50,
  ip: true,
  overrides: {
    '192.168.1.1': {
      rate: 0,        // unlimited
      burst: 0
    }
  }
}));
server.use(function setETag(req, res, next) {
  res.header('ETag', 'myETag');
  res.header('Last-Modified', new Date());
});
server.use(restify.conditionalRequest());
server.on('after', restify.auditLogger({
  log: bunyan.createLogger({
    name: 'audit',
    stream: process.stdout
  })
}));
```

### 9). Request API

对node内核API：http.ServerRequest的封装

### 10). Response API

对node内核API：http.ServerResponse的封装

## 5. restify客户端API

* JsonClient: 收application/json, 发application/json
* StringClient: 收text/plain, 发url-encoded request
* HttpClient: 封装http/https

新建服务器端测试程序

```{bash}

var restify = require('restify');
var server = restify.createServer();
server.use(restify.acceptParser(server.acceptable));
server.use(restify.authorizationParser());
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.jsonp());
server.use(restify.gzipResponse());
server.use(restify.bodyParser());

# 用于处理JsonClient请求
server.get('/json/v1',function(req,res,next){
  var a = {name:'conan',blog:'blog.fens.me'}
  res.send(a);
});

# 用于处理StringClient请求
server.get('/json/v2',function(req,res,next){
  var a = {name:'conan',blog:'blog.fens.me'}
  res.send(JSON.stringify(a));
});


server.listen(3900, function() {
  console.log('%s listening at %s', server.name, server.url);
});
```

### 1). JsonClient

curl请求

```{bash}
~ curl -i http://localhost:3900/json/v1

HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 38
Date: Mon, 13 Jan 2014 12:09:36 GMT
Connection: keep-alive

{"name":"conan","blog":"blog.fens.me"}
```

创建文件：jsonclient.js

```{bash}
~ vi jsonclient.js

var restify = require('restify');

var client = restify.createJsonClient({
  url: 'http://localhost:3900'
});

client.get('/json/v1', function(err, req, res, obj) {
  if(err) console.log(err)
  console.log(JSON.stringify(obj, null, 2));
});
```

运行程序：

```{bash}
~ node jsonclient.js
{
  "name": "conan",
  "blog": "blog.fens.me"
}
```

### 2). StringClient

curl请求

```{bash}
~ curl -i http://localhost:3900/json/v2 -H 'accept:text/plain'

HTTP/1.1 200 OK
Content-Type: text/plain
Content-Length: 38
Date: Mon, 13 Jan 2014 12:10:07 GMT
Connection: keep-alive

{"name":"conan","blog":"blog.fens.me"}
```

创建文件：stringclient.js

```{bash}
~ vi stringclient.js

var restify = require('restify');

var client = restify.createStringClient({
  url: 'http://localhost:3900'
});

client.get('/json/v2', function(err, req, res, obj) {
  if(err) console.log(err)
  console.log(JSON.stringify(obj, null, 2));
});
```

运行程序：

```{bash}
~ node stringclient.js
"{\"name\":\"conan\",\"blog\":\"blog.fens.me\"}"
```

### 3). HttpClient

创建文件: httpclient.js

```{bash}
~ vi httpclient.js

var restify = require('restify');

var client = restify.createClient({
  url: 'http://localhost:3900'
});

client.get('/json/v1', function(err, req) {

  req.on('result', function(err, res) {
    res.body = '';
    res.setEncoding('utf8');
    res.on('data', function(chunk) {
      res.body += chunk;
    });

    res.on('end', function() {
      console.log(res.body);
    });
});
});
```

运行程序

```{bash}
~ node httpclient.js
{"name":"conan","blog":"blog.fens.me"}
```

我们已经全面了解的了restify包，接下就可以快速构建我们自己的REST服务了。Node实现如此之简单，让原来Java程序员如何生存呢？！

#### 转载请注明出处：http://blog.fens.me/nodejs-restify/


