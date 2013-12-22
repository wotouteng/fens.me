Nodejs基础中间件Connect
==========

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-connect/

![Nodejs基础中间件Connect](http://blog.fens.me/wp-content/uploads/2013/09/connect.png)

#### 前言

“中间件”在软件领域是一个非常广的概念，除操作系统的软件都可以称为中间件，比如，消息中间件，ESB中间件，日志中间件，数据库中间件等等。

Connect被定义为Node平台的中间件框架，从定位上看Connect一定是出众的，广泛兼容的，稳定的，基础的平台性框架。如果攻克Connect，会有助于我们更了解Node的世界。Express就是基于Connect开发的。

让我们开始探索Connect中间件。

#### 目录

1. Connect介绍
2. Connect安装
3. Connect内置中间件介绍
4. logger
5. cookieParser
6. session
7. cookieSession
8. compress
9. basicAuth
10. bodyParser
11. json
12. urlencoded
13. multipart
14. timeout
15. reponseTime
16. methodOverride
17. csrf
18. static
19. staticCache
20. directory
21. vhost
22. favicon
23. limit
24. query
25. errorHadnler

## 1. Connect介绍

Connect是一个node中间件（middleware）框架。如果把一个http处理过程比作是污水处理，中间件就像是一层层的过滤网。每个中间件在http处理过程中通过改写request或（和）response的数据、状态，实现了特定的功能。这些功能非常广泛，下图列出了connect所有内置中间件和部分第三方中间件。 这里能看到[完整的中间件列表](https://github.com/senchalabs/connect/wiki)。

下图根据中间件在整个http处理流程的位置，将中间件大致分为3类：

1. Pre-Request 通常用来改写request的原始数据
2. Request/Response 大部分中间件都在这里，功能各异
3. Post-Response 全局异常处理，改写response数据等

![](http://blog.fens.me/wp-content/uploads/2013/09/connect-middleware.png)

关于Connect介绍部分，摘自：http://www.cnblogs.com/luics/archive/2012/11/28/2775206.html

## 2. Connect安装

### 我的系统环境

+ win7 64bit
+ Nodejs:v0.10.5
+ Npm:1.2.19

通过nodejs安装Connect

```{bash}

~ D:\workspace\javascript>mkdir nodejs-connect && cd nodejs-connect
~ D:\workspace\javascript\nodejs-connect> npm install connect
connect@2.9.0 node_modules\connect
├── methods@0.0.1
├── uid2@0.0.2
├── pause@0.0.1
├── cookie-signature@1.0.1
├── fresh@0.2.0
├── qs@0.6.5
├── bytes@0.2.0
├── buffer-crc32@0.2.1
├── cookie@0.1.0
├── debug@0.7.2
├── send@0.1.4 (range-parser@0.0.4, mime@1.2.11)
└── multiparty@2.1.8 (stream-counter@0.1.0, readable-stream@1.0.17)
```

### 尝试做一个最简单的web服务器

增加一个文件：app.js

```{bash}
var connect = require('connect');
var app = connect()
    .use(connect.logger('dev'))
    .use(function (req, res) {
        res.end('hello world\n');
    })
    .listen(3000);
```

启动node

```{bash}
~ D:\workspace\javascript\nodejs-connect>node app.js
GET / 200 5ms
GET /favicon.ico 200 0ms
```

打开浏览器：http://localhost:3000/

![](http://blog.fens.me/wp-content/uploads/2013/09/connect-web.png)

## 3. Connect内置中间件介绍

### 22个内置中间件列表

+ logger: 用户请求日志中间件
+ csrf: 跨域请求伪造保护中间件
+ compress: gzip压缩中间件
+ basicAuth: basic认证中间件
+ bodyParser: 请求内容解析中间件
+ json: JSON解析中间件
+ urlencoded: application/x-www-form-urlencode请求解析中间件
+ multipart: multipart/form-data请求解析中间件
+ timeout: 请求超时中间件
+ cookieParser: cookie解析中间件
+ session: 会话管理中间件
+ cookieSession: 基于cookies的会话中间件
+ methodOverride: HTTP伪造中间件
+ reponseTime: 计算响应时间中间件
+ staticCache: 缓存中间件
+ static: 静态文件处理中间件
+ directory: 目录列表中间件
+ vhost: 虚拟二级域名映射中间件
+ favicon: 网页图标中间件
+ limit: 请求内容大小限制中间件
+ query: URL解析中间件
+ errorHadnler: 错误处理中间件

下面将分别介绍这22个中间件。

## 4. logger

描述：用来输出用户请求日志。

参数：options或者format字符串

### options:

+ format:参考下面的tokens
+ stream:输出流，默认是stdout
+ buffer:缓冲时间，默认是1000ms
+ immediate:立刻打印日志

### tokens: format格式

+ :req[header] ex: :req[Accept]
+ :res[header] ex: :res[Content-Length]
+ :http-version
+ :response-time
+ :remote-addr
+ :date
+ :method
+ :url
+ :referrer
+ :user-agent
+ :status

###Formats:缩写

+ default ‘:remote-addr – - [:date] “:method :url HTTP/:http-version” :status :res[content-length] “:referrer” “:user-agent”‘
+ short ‘:remote-addr – :method :url HTTP/:http-version :status :res[content-length] – :response-time ms’
+ tiny ‘:method :url :status :res[content-length] – :response-time ms’
+ dev concise output colored by response status for development use

例子：新建logger.js

```{bash}

var connect = require('connect');
var app = connect()
    .use(connect.logger())
    .use(function (req, res) {
        res.end('hello world\n');
    })
    .listen(3000);
connect.logger()


127.0.0.1 - - [Mon, 23 Sep 2013 05:14:18 GMT] "GET / HTTP/1.1" 200 - "-" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKi
t/537.36 (KHTML, like Gecko) Chrome/28.0.1500.95 Safari/537.36"
127.0.0.1 - - [Mon, 23 Sep 2013 05:14:18 GMT] "GET /favicon.ico HTTP/1.1" 200 - "-" "Mozilla/5.0 (Windows NT 6.1; WOW64)
 AppleWebKit/537.36 (KHTML, like Gecko) Chrome/28.0.1500.95 Safari/537.36"
```

connect.logger(‘short’)

```{bash}
127.0.0.1 - GET / HTTP/1.1 200 - - 9 ms
127.0.0.1 - GET /favicon.ico HTTP/1.1 200 - - 1 ms
```

connect.logger(‘dev’)

```{bash}
GET / 200 5ms
GET /favicon.ico 200 1ms
```

connect.logger(function(tokens, req, res){ return ‘some format string’ })

```{bash}
some format string
some format string
```

所以在开发环境，我们日志设置成dev就好了。

## 5. cookieParser

描述:cookie解析中间件，解析Cookies的头通过req.cookies得到cookies。还可以通过req.secret加密cookies。

例子：新建cookieParser.js

```{bash}
var connect = require('connect');
var app = connect()
    .use(connect.logger('dev'))
    .use(connect.cookieParser('secret string'))
    .use(function (req, res, next) {
        req.cookies.website="blog.fens.me";
        res.end(JSON.stringify(req.cookies));
    })
    .listen(3000);
```

![](http://blog.fens.me/wp-content/uploads/2013/09/connect-cookiesParser.png)

## 6. session

描述：会话管理中间件

依赖：cookieParser

参数：options

### options:

+ key:Cookies名，默认值为connect.sid
+ store: session存储实例
+ secret: session的cookie加密
+ cookie: session的cookie配置，默认值为{path: ‘/’, httpOnly: true, maxAge: null}
+ proxy:安全cookie的反向代理，通过x-forwarded-proto实现

### Cookie option:

cookie.maxAge: 默认值null，表示当浏览器关闭后cookie被删除。

例子：新建session.js

```{bash}

var connect = require('connect');
var app = connect()
    .use(connect.logger('dev'))
    .use(connect.cookieParser())
    .use(connect.session({secret: '123', cookie: { maxAge: 60000 }}))
    .use(function (req, res, next) {
        if(req.session.pv){
            res.setHeader('Content-Type', 'text/html');
            res.write('views: ' + req.session.pv);
            res.end();
            req.session.pv++;
        }else{
            req.session.pv = 1;
            res.end('Refresh');
        }

    })
    .listen(3000);
```

![](http://blog.fens.me/wp-content/uploads/2013/09/connect-session.png)

## 7. cookieSession

描述:基于cookies的会话中间件

参数：options:

### options:

+ key:Cookies名，默认值为connect.sess
+ secret: 防止cookie窃取
+ cookie: session的cookie配置，默认值为{path: ‘/’, httpOnly: true, maxAge: null}
+ proxy:安全cookie的反向代理，通过x-forwarded-proto实现

### Clearing sessions:

req.session = null;

例子：新建cookieSession.js

```{bash}

var connect = require('connect');
var connect = require('connect');
var app = connect()
    .use(connect.logger('dev'))
    .use(connect.cookieParser())
    .use(connect.cookieSession({ secret: 'adddaa!', cookie: { maxAge: 60 * 60 * 1000 }}))
    .use(function (req, res, next) {
        if(req.session.pv){
            res.setHeader('Content-Type', 'text/html');
            res.write('views: ' + req.session.pv );
            req.session.pv++;
            console.log(req.session.pv);
            res.end();
        }else{
            req.session.pv = 1;
            res.end('Refresh');
        }

    })
    .listen(3000);
```

![](http://blog.fens.me/wp-content/uploads/2013/09/connect-cookieSession.png)

我们发现，这次不管刷新多少次页面，req.session.pv始终是1.

## 8. compress

描述:gzip压缩中间件，通过filter函数设置，需要压缩的文件类型。压缩算法为gzip/deflate。

filter函数

```{bash}
exports.filter = function(req, res){
  return /json|text|javascript|dart|image\/svg\+xml|application\/x-font-ttf|application\/vnd\.ms-opentype|application\/vnd\.ms-fontobject/.test(res.getHeader('Content-Type'));
};
```

Threshold压缩阈值：当响应请求大于阈值，则压缩响应请求。

参数：options

+ chunkSize (default: 16*1024)
+ windowBits
+ level: 0-9 where 0 is no compression, and 9 is slow but best compression
+ memLevel: 1-9 low is slower but uses less memory, high is fast but uses more
+ strategy: compression strategy

例子：新建compress.js

```{bash}

var connect = require('connect');
var app = connect()
    .use(connect.logger('dev'))
    .use(connect.compress({level:9}))
    .use(function (req, res) {
        res.setHeader('Content-Type', 'text/html');
        res.write(res);
        res.end('hello world\n');
    })
    .listen(3000);
```

![](http://blog.fens.me/wp-content/uploads/2013/09/connect-compress.png)

## 9. basicAuth

描述:basic认证中间件，实现简单的用户密码登陆，当用户和密码验证通过后，通过一个callback方法继续执行。

同步验证：

```{bash}
connect()
  .use(connect.basicAuth(function(user, pass){
    return 'tj' == user && 'wahoo' == pass;
  }))
```

异步验证：

```{bash}

connect()
  .use(connect.basicAuth(function(user, pass, fn){
    User.authenticate({ user: user, pass: pass }, fn);
  }))
```

例子：新建basicAuth.js

```{bash}
var connect = require('connect');
var app = connect();
app.use(connect.logger('dev'));

//  基本用法
//  app.use(connect.basicAuth('fens','fens'))

//  同步验证
app.use(connect.basicAuth(function (user, pass) {
    var isLogin = 'fens' == user && 'fens' == pass;
    console.log("Login:" + isLogin);
    return isLogin;
}))
app.use(function (req, res) {
    res.end('hello world\n');
})
app.listen(3000);
```

验证弹出框

![](http://blog.fens.me/wp-content/uploads/2013/09/connect-basicAuth1.png)

正确输入用户和密码后，访问页面

![](http://blog.fens.me/wp-content/uploads/2013/09/connect-basicAuth2.png)

## 10. bodyParser

描述:请求内容解析中间件，支持多种类型application/json,

application/x-www-form-urlencoded, multipart/form-data.

与其他的3个中间件相同：

```{bash}
app.use(connect.json());
app.use(connect.urlencoded());
app.use(connect.multipart());
```

例子：新建bodyParser.js

```{bash}

var connect = require('connect');
var app = connect()
    .use(connect.logger('dev'))
    .use(connect.bodyParser())
    .use(function(req, res) {
        res.end('req.body=>' + JSON.stringify(req.body));
    })
    .listen(3000);
```

POST方法:

```{bash}
~ curl -d 'user[name]=tj' http://localhost:3000/
req.body=>{"'user":{"name":"tj'"}}
```

GET方法:

```{bash}
~ curl http://localhost:3000/?user=123
req.body=>{}
```

## 11. json

描述:JSON解析中间件，req.body传值

参数：options

+ strict: when false anything JSON.parse() accepts will be parsed
+ reviver: used as the second “reviver” argument for JSON.parse
+ limit: byte limit [1mb]

同bodyParser。

## 12. urlencoded

描述:application/x-www-form-urlencode请求解析中间件

参数：options

+ limit: byte limit [1mb]

同bodyParser。

## 13. multipart

描述:multipart/form-data请求解析中间件，解析req.body和req.files.

上传文件：uploadDir

```{bash}
app.use(connect.multipart({ uploadDir: path }))
```

参数：options

+ limit: byte limit defaulting to [100mb]
+ defer: 在不等待“结束”事件，通过调用req.form.next()函数，可以缓冲并处理多个表单对象，还可以绑定到“progress”或“events”的事件。

Temporary Files:临时文件

默认情况下，临时文件会被保存在os.tmpDir()目录，但不会自动回归，我们必须手动处理。如果没有使用defer选项时，你可以通过req.files来获得对象的使用。

```{bash}

req.files.images.forEach(function(file){
  console.log('  uploaded : %s %skb : %s', file.originalFilename, file.size / 1024 | 0, file.path);
});
```

Streaming:流式处理

当使用defer选项时，文件在上传过程中，你可以通过”part”事件和流控制访问文件。

```{bash}
req.form.on('part', function(part){
  // transfer to s3 etc
  console.log('upload %s %s', part.name, part.filename);
  var out = fs.createWriteStream('/tmp/' + part.filename);
  part.pipe(out);
});

req.form.on('close', function(){
  res.end('uploaded!');
});
```

例子：新建multipart.js

```{bash}
var connect = require('connect');
var app = connect()
.use(connect.logger('dev'))
.use(connect.multipart({ uploadDir: 'd:\\tmp' }))
.use(function (req, res) {
if(req.method=='POST'){
console.log(req.files);
res.end('Upload ==>'+ req.files.file.path);
}
res.setHeader('Content-Type', 'text/html');
res.write('<form enctype="multipart/form-data" method="POST"><input type="file" name="file">');
res.write('<input type="submit" value="submit"/>');
res.write('</form>');
res.end('hello world\n');
})
.listen(3000);
```

通过form表单选择文件

![](http://blog.fens.me/wp-content/uploads/2013/09/connect-multipart1.png)

POST请求解析

![](http://blog.fens.me/wp-content/uploads/2013/09/connect-multipart2.png)

## 14. timeout

描述:请求超时中间件，默认超时时间是5000ms，可以清除这个时间通过req.clearTimeout()函数。超时的错误，可以通过next()函数传递。我们也可以设置超时的响应错误状态码：.timeout=503

例子：新建timeout.js，模拟超时

```{bash}
var connect = require('connect');
var app = connect()
    .use(connect.logger('dev'))
    .use(connect.timeout(1000))
    .use(function (req, res) {
        setTimeout(function(){
            res.end('hello world\n');
        },5000)
    })
    .listen(3000);
```

控制台输出：

```{bash}

Error: Response timeout
    at IncomingMessage. (D:\workspace\javascript\nodejs-connect\node_modules\connect\lib\middleware\timeout.j
s:39:17)
    at IncomingMessage.EventEmitter.emit (events.js:95:17)
    at null._onTimeout (D:\workspace\javascript\nodejs-connect\node_modules\connect\lib\middleware\timeout.js:34:11)
    at Timer.listOnTimeout [as ontimeout] (timers.js:110:15)
GET / 503 1030ms - 389b
Error: Response timeout
    at IncomingMessage. (D:\workspace\javascript\nodejs-connect\node_modules\connect\lib\middleware\timeout.j
s:39:17)
    at IncomingMessage.EventEmitter.emit (events.js:95:17)
    at null._onTimeout (D:\workspace\javascript\nodejs-connect\node_modules\connect\lib\middleware\timeout.js:34:11)
    at Timer.listOnTimeout [as ontimeout] (timers.js:110:15)
GET /favicon.ico 503 1006ms - 389b
```

## 15. reponseTime

描述:计算响应时间中间件，在response的header增加X-Response-Time，计算响应时间。

例子：新建reponseTime.js

```{bash}

var connect = require('connect');
var app = connect()
    .use(connect.logger('dev'))
    .use(connect.responseTime())
    .use(function (req, res) {
        res.end('hello world\n');
    })
    .listen(3000);
```

![](http://blog.fens.me/wp-content/uploads/2013/09/connect-responseTime.png)

## 16. methodOverride

无法模拟出效果，暂时先跳过

描述: 提供伪造HTTP中间件，检查一个method是否被重写，通过传递一个key，得到_method，原始的方法通过req.originalMethod获得。

例子：新建methodOverride.js

```{bash}
var connect = require('connect');
var app = connect()
    .use(connect.logger('dev'))
    .use(connect.methodOverride('KEY'))
    .use(function (req, res) {
        res.end(JSON.stringify(req.headers));
    })
    .listen(3000);
```

## 17. csrf

描述:跨域请求csrf保护中间件，通过req.csrfToken()令牌函数绑定到请求的表单字段。这个令牌会对访客会话进行验证。

默认情况会检查通过bodyParser()产生的req.body，query()函数产生的req.query，和X-CSRF-Token的header。

依赖：session(), cookieParser()

参数：options:

+ value: 一个函数接受请求，返回的令牌

例子：新建csrf.js

```{bash}

var connect = require('connect');
var app = connect()
    .use(connect.logger('dev'))
    .use(connect.cookieParser())
    .use(connect.session({secret: '123', cookie: { maxAge: 60000 }}))
    .use(connect.csrf({value:'admin'}))
    .use(function (req, res) {
        res.setHeader('Content-Type', 'text/html');
        res.end('hello world\n');
        console.log(req.csrfToken());
    })
    .listen(3000);
```

生成的csrf Token:

```{bash}
1YZ72JuGRTOh/mzqByktPoyz2C+Dk1E5wXyj0=
GET / 200 356ms
bItSjAAXydK4jkYYLDnc1c0+7AGDFwGX6r8ns=
GET /favicon.ico 200 3ms
```

## 18. static

描述: 静态文件处理中间件，设置root路径作为静态文件服务器

参数：options:

options:

+ maxAge:浏览器缓存存活时间(毫秒)，默认值0
+ hidden:是否允许传递隐藏类型的文件，默认值false
+ redirect:是否允许当访问名是一个目录，结尾增加”/”，默认值true
+ index:设置默认的文件名,默认值index.html

例子：新建static.js

```{bash}

var connect = require('connect');
var app = connect()
    .use(connect.logger('dev'))
    .use(connect.static(__dirname+'/static',{maxAge:60*60*1000,hidden:false}))
    .use(function (req, res) {
        res.setHeader('Content-Type', 'text/html');
        res.write('static:');
        res.write('');
        res.write('hidden:');
        res.end('');
    })
    .listen(3000);
```

隐藏类型的文件，没有被显示出来。

![](http://blog.fens.me/wp-content/uploads/2013/09/connect-static.png)

## 19. staticCache

描述: 静态文件缓存中间件，最大可以缓存128个对象，每个对象最大256K，总加起来32mb。

缓存算法采用LRU(最近最少使用)算法，让最活跃的对象保存在缓存中，从而增加命中。

Benchmarks：性能测试

+ static(): 2700 rps
+ node-static: 5300 rps
+ static() + staticCache(): 7500 rps

依赖：static()

参数：options:

+ maxObjects:最多能缓存的对象，默认值128个
+ maxLength:最大缓存的对象，默认值256kb

例子：新建staticCache.js

```{bash}

var connect = require('connect');
var app = connect()
.use(connect.logger('dev'))
.use(connect.static(__dirname+'/static',{maxAge:60*60*1000,hidden:false}))
.use(connect.staticCache())
.use(function (req, res) {
res.setHeader('Content-Type', 'text/html');
res.write('static:');
res.write('<img src="static.png" width="100px"/>');
res.write('hidden:');
res.end('<img src=".png" width="100px"/>');
})
.listen(3000);
```

控制台日志：

```{bash}
connect.staticCache() is deprecated and will be removed in 3.0
use varnish or similar reverse proxy caches.
GET / 200 11ms
GET /.png 200 0ms
```

建议用varnish或专门的缓存工具，来代替staticCache()。

## 20. directory

描述: 目录列表中间件，列出指定目录下的文件

参数：options:

+ hidden:是否显示隐藏文件，默认值false.
+ icons:是否显示网站图标，默认值false.
+ filter:是否过滤文件，默认值false.

例子：新建directory.js

```{bash}
var connect = require('connect');
var app = connect()
    .use(connect.logger('dev'))
    .use(connect.directory(__dirname+'/static',{hidden:true}))
    .use(function (req, res) {
        res.end();
    })
    .listen(3000);
```

![](http://blog.fens.me/wp-content/uploads/2013/09/connect-directory.png)

## 21. vhost

无法模拟出效果，暂时先跳过

描述: 虚拟二级域名映射中间件，设置hostname和server。

例子：新建vhost.js

```{bash}
var connect = require('connect');
var app = connect();
app.use(connect.logger('dev'));
app.use(function (req, res) {
    res.end(JSON.stringify(req.headers.host));
});

var fooApp = connect();
fooApp.use(connect.logger('dev'));
fooApp.use(function (req, res) {
    res.end('hello fooApp\n');
});

var barApp = connect();
barApp.use(connect.logger('dev'));
barApp.use(function (req, res) {
    res.end('hello barApp\n');
});

app.use(connect.vhost('foo.com', fooApp));
app.use(connect.vhost('bar.com', barApp));

app.listen(3000);
```

## 22. favicon

描述:网页图标中间件，指定favicon的路径

参数：options:

+ maxAge:缓存存活时间(毫秒)，默认值1天

例子：新建favicon.js

```{bash}
var connect = require('connect');
var app = connect()
    .use(connect.favicon('static/favicon.ico'))
    .use(connect.logger('dev'))
    .use(function (req, res) {
        res.end('hello world\n');
    })
    .listen(3000);
```

网站图标

![](http://blog.fens.me/wp-content/uploads/2013/09/connect-favicon.png)

## 23. limit

描述: 请求内容大小限制中间件，可以用mb,kb,gb表示单位。

例子：新建limit.js

```{bash}
var connect = require('connect');
var app = connect()
.use(connect.logger('dev'))
.use(connect.limit('1mb'))
.use(connect.multipart({ uploadDir: 'd:\\tmp' }))
.use(function (req, res) {
if(req.method=='POST'){
console.log(req.files);
res.end('Upload ==>'+ req.files.file.path);
}
res.setHeader('Content-Type', 'text/html');
res.write('<form enctype="multipart/form-data" method="POST"><input type="file" name="file">');
res.write('<input type="submit" value="submit"/>');
res.write('</form>');
res.end('hello world\n');
})
.listen(3000);
```

控制台日志，上传大于1mb的文件。

```{bash}
GET / 200 8ms
Error: Request Entity Too Large
    at Object.exports.error (D:\workspace\javascript\nodejs-connect\node_modules\connect\lib\utils.js:62:13)
    at Object.limit [as handle] (D:\workspace\javascript\nodejs-connect\node_modules\connect\lib\middleware\limit.js:46:
47)
    at next (D:\workspace\javascript\nodejs-connect\node_modules\connect\lib\proto.js:190:15)
    at Object.logger (D:\workspace\javascript\nodejs-connect\node_modules\connect\lib\middleware\logger.js:156:5)
    at next (D:\workspace\javascript\nodejs-connect\node_modules\connect\lib\proto.js:190:15)
    at Function.app.handle (D:\workspace\javascript\nodejs-connect\node_modules\connect\lib\proto.js:198:3)
    at Server.app (D:\workspace\javascript\nodejs-connect\node_modules\connect\lib\connect.js:65:37)
    at Server.EventEmitter.emit (events.js:98:17)
    at HTTPParser.parser.onIncoming (http.js:2027:12)
    at HTTPParser.parserOnHeadersComplete [as onHeadersComplete] (http.js:119:23)
POST / 413 8ms - 961b
```

## 24. query

描述:URL解析中间件，自动解析URL查询参数req.query

参数：options,qs.parse函数

例子：新建query.js

```{bash}

var connect = require('connect');
var app = connect()
    .use(connect.query())
    .use(connect.logger('dev'))
    .use(function (req, res) {
        res.end(JSON.stringify(req.query));
    })
    .listen(3000);
```

通过CURL测试：

```{bash}
curl -d '{name:'aad'}' http://localhost:3000?pass=did
{"pass":"did"}
```

req.query会自动解析URL的查询参数，不解析POST的数据。

## 25. errorHadnler

无法模拟出效果，暂时先跳过

描述:错误处理中间件，对于开发过程中的错误，提供栈跟踪和错误响应，接受3种类型text,html,json。

Text: text/plain是默认类型，返回一个简单的栈跟踪和错误消息

JSON：application/json，返回{‘error’:error}对象

HTML: 返回一个HTML错误页面

参数：options:

+ showStack:返回错误信息和错误栈.默认值false
+ showMessage,只返回错误信息，默认值false
+ dumpExceptions, 输出异常日志，默认值false
+ logErrors,输出错误日志到文件，默认值false

例子：新建errorHadnler.js

```{bash}

var connect = require('connect');
var app = connect()
    .use(connect.logger())
    .use(connect.errorHandler({ dumpExceptions: true, showStack: true }))
    .use(function (req, res) {
        req.headers.accept='html';
        res.write(JSON.stringify(req.headers.accept));
        throw new Error('my errorHandler!!!');
        res.end('Hello');
    })
    .listen(3000);
```

控制台输出

```{bash}
Error: my errorHandler!!!
    at Object.handle (D:\workspace\javascript\nodejs-connect\errorHadnler.js:8:15)
    at next (D:\workspace\javascript\nodejs-connect\node_modules\connect\lib\proto.js:190:15)
    at next (D:\workspace\javascript\nodejs-connect\node_modules\connect\lib\proto.js:192:9)
    at Object.logger (D:\workspace\javascript\nodejs-connect\node_modules\connect\lib\middleware\logger.js:156:5)
    at next (D:\workspace\javascript\nodejs-connect\node_modules\connect\lib\proto.js:190:15)
    at Function.app.handle (D:\workspace\javascript\nodejs-connect\node_modules\connect\lib\proto.js:198:3)
    at Server.app (D:\workspace\javascript\nodejs-connect\node_modules\connect\lib\connect.js:65:37)
    at Server.EventEmitter.emit (events.js:98:17)
    at HTTPParser.parser.onIncoming (http.js:2027:12)
    at HTTPParser.parserOnHeadersComplete [as onHeadersComplete] (http.js:119:23)
```

#### 转载请注明出处：http://blog.fens.me/nodejs-connect/









