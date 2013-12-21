玩转Nodejs日志管理log4js
============

#### 从零开始nodejs系列文章

从零开始nodejs系列文章，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-crawler-douban/

#### 前言

日志对任何的应用来说都是至关重要的。在Nodejs中使用express框架并没有自带的日志模块，我们可以选择log4js来完成日志记录的功能。

如果用过JAVA中log4j的同学，肯定对日志并不陌生，学习log4js会更得心应手的。

![玩转Nodejs日志管理log4js](http://blog.fens.me/wp-content/uploads/2013/06/log4js.png)

#### 文章目录：

1. 默认的控制台输出
2. 通过log4js输出日志
3. 配置log4js与express框架集成
4. 根据项目配置log4js
5. 优化log4js结构

## 1. 默认的控制台输出

我们使用express框架时，开发模式用node或者supervisor启动nodejs应用时，控制台都是显示如下的日志。

```{bash}

GET /css/bootstrap.min.css 304 1ms
GET /css/my.css 304 0ms
GET /js/bootstrap.min.js 304 4ms
GET /js/jquery-1.9.1.min.js 304 6ms
GET /js/holder.js 304 3ms
GET /cat/json/latest 200 6ms
GET /cat/json/master 200 4ms
GET /cat/json/classic 200 2ms
GET /about 200 6ms
GET /css/bootstrap.min.css 304 2ms
GET /css/my.css 304 2ms
GET /js/bootstrap.min.js 304 2ms
GET /js/jquery-1.9.1.min.js 304 1ms
GET /js/holder.js 304 1ms
GET /js/bootstrap.min.js 304 1ms
GET / 304 6ms
GET /js/jquery-1.9.1.min.js 304 2ms
GET /css/my.css 304 1ms
GET /css/bootstrap.min.css 304 1ms
GET /js/bootstrap.min.js 304 2ms
GET /js/holder.js 304 2ms
GET /cat/json/latest 200 3ms
GET /cat/json/master 200 2ms
GET /cat/json/classic 200 2ms
GET /admin/ 304 13ms
GET /css/bootstrap.min.css 304 3ms
GET /js/jquery-1.9.1.min.js 304 2ms
GET /css/my.css 304 2ms
GET /js/bootstrap.min.js 304 1ms
GET /js/holder.js 304 2ms
```
我们也可以在代码中，用console.log()打印一些控制台日志。

修改routes/index.js

```{bash}

exports.index = function(req, res){
	console.log("This is an index page!");
	res.render('index', {
  		title:'首页|moive.me',
  		page:'index'
  	});
};
```

访问页面，结果如下。

```{bash}
This is an index page!
GET / 304 19ms
GET /css/bootstrap.min.css 304 4ms
GET /css/my.css 304 2ms
GET /js/jquery-1.9.1.min.js 304 38ms
GET /js/holder.js 304 29ms
GET /js/bootstrap.min.js 304 28ms
```

这样的输出的结果，都是在控制台显示，一旦server重启日志就丢失了。对于程序开发来说，这样的输出已经够用了。但是在生产环境上，我们希望能把控制台的输出，保存到文件中，而且需要更多的信息，不仅仅是默认的简化的日志信息。

由于express框架没有日志功能，我们需要引入log4js包来完成这个功能。

## 2. 通过log4js输出日志

我们先可看一下，通过log4js输出的日志是什么样子的，下一节再介绍具体的配置。

```{bash}

This is an index page!
GET / 304 17ms
[2013-06-19 17:45:55.981] [INFO] normal - 127.0.0.1 - - "GET / HTTP/1.1" 304 - "http://localhost:3000/admin/" "Mozilla/5
.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.36"
GET /css/bootstrap.min.css 304 10ms
[2013-06-19 17:45:56.015] [INFO] normal - 127.0.0.1 - - "GET /css/bootstrap.min.css HTTP/1.1" 304 - "http://localhost:30
00/admin/crawler/youku" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110
 Safari/537.36"
GET /css/my.css 304 8ms
[2013-06-19 17:45:56.017] [INFO] normal - 127.0.0.1 - - "GET /css/my.css HTTP/1.1" 304 - "http://localhost:3000/admin/cr
awler/youku" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537
.36"
GET /js/jquery-1.9.1.min.js 304 19ms
[2013-06-19 17:45:56.031] [INFO] normal - 127.0.0.1 - - "GET /js/jquery-1.9.1.min.js HTTP/1.1" 304 - "http://localhost:3
000/admin/" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.
36"
GET /js/bootstrap.min.js 304 13ms
[2013-06-19 17:45:56.037] [INFO] normal - 127.0.0.1 - - "GET /js/bootstrap.min.js HTTP/1.1" 304 - "http://localhost:3000
/admin/" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.36"

GET /js/holder.js 304 20ms
[2013-06-19 17:45:56.040] [INFO] normal - 127.0.0.1 - - "GET /js/holder.js HTTP/1.1" 304 - "http://localhost:3000/admin/
" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.36"
```

相同的请求，控制台输出的结果多了很多，完整的web服务器的日志格式。
这才是生产环境需要的！信息更丰富，并且与nginx和apache是一样的格式。

## 3. 配置log4js与express框架集成

下载log4js包

```{bash}

~ npm install log4js
log4js@0.6.6 node_modules\log4js
├── dequeue@1.0.3
├── semver@1.1.4
├── async@0.1.15
└── readable-stream@1.0.2
```

修改app.js

```{bash}

~ vi app.js

var log4js = require('log4js');
log4js.configure({
  appenders: [
    { type: 'console' }, //控制台输出
    {
      type: 'file', //文件输出
      filename: 'logs/access.log', 
      maxLogSize: 1024,
      backups:3,
      category: 'normal' 
    }
  ]
});
var logger = log4js.getLogger('normal');
logger.setLevel('INFO');
...
//app.use(...)
//app.use(...)
app.use(log4js.connectLogger(logger, {level:log4js.levels.INFO}));
app.use(app.router);
```

我需要在app.js中进行log4js的配置。

appenders中配置了两个输出，一个是控制台输出，一个是文件输出。

appenders.type=file的对象,指定文件输出位置及文件大小，当超过maxLogSize大小时，会自动生成一个新文件。logs的文件目录要动手创建。
level:log4js.levels.INFO, 设置默认日志输出级别是INFO。

### log4js的输出级别6个: trace, debug, info, warn, error, fatal

+ logger.trace(‘Entering cheese testing’);
+ logger.debug(‘Got cheese.’);
+ logger.info(‘Cheese is Gouda.’);
+ logger.warn(‘Cheese is quite smelly.’);
+ logger.error(‘Cheese is too ripe!’);
+ logger.fatal(‘Cheese was breeding ground for listeria.’);

如果输出级别是INFO，则不会打印出低于info级别的日志trace,debug，只打印info,warn,error,fatal。这样做的好处在于，在生产环境中我们可能只关心异常和错误，并不关心调试信息。从而大大减少日志的输出，能减少磁盘写入。而在开发环境中，我们可以需要打印非常多的信息，帮助开发人员定位错误，调试代码。

还有一个好处就是，代码中可以混有各种的日志打印代码。我们只要在一个配置文件中，修改输出级别，日志输出就会发生变化，不用修改所有的代码。如果所有地方都是console.log()，那么上线的时候，改动这个东西就要花很多时间。

## 4. 根据项目配置log4js

上一节中，介绍了log4js和express集成。但默认的配置可能并不合适我们的项目，还需要对log4js的参数进行一些调整。

### 1. 代替console.log()
增加replaceConsole配置，让所有console输出到日志中，以[INFO] console代替console默认样式。

```{bash}
~ vi app.js
var log4js = require('log4js');
log4js.configure({
  appenders: [
    { type: 'console' },{
      type: 'file', 
      filename: 'logs/access.log', 
      maxLogSize: 1024,
      backups:4,
      category: 'normal' 
    }
  ],
  replaceConsole: true
});
```

查看输出结果：

```{bash}
[2013-06-19 18:18:41.997] [INFO] console - This is an index page!
GET / 304 15ms
[2013-06-19 18:18:42.010] [INFO] normal - 127.0.0.1 - - "GET / HTTP/1.1" 304 - "http://localhost:3000/admin/" "Mozilla/5
.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.36"
GET /css/bootstrap.min.css 304 5ms
[2013-06-19 18:18:42.042] [INFO] normal - 127.0.0.1 - - "GET /css/bootstrap.min.css HTTP/1.1" 304 - "http://localhost:30
00/admin/crawler/youku" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110
 Safari/537.36"
GET /css/my.css 304 11ms
[2013-06-19 18:18:42.051] [INFO] normal - 127.0.0.1 - - "GET /css/my.css HTTP/1.1" 304 - "http://localhost:3000/admin/cr
awler/youku" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537
.36"
GET /js/jquery-1.9.1.min.js 304 35ms
[2013-06-19 18:18:42.089] [INFO] normal - 127.0.0.1 - - "GET /js/jquery-1.9.1.min.js HTTP/1.1" 304 - "http://localhost:3
000/admin/" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.
36"
GET /js/holder.js 304 42ms
[2013-06-19 18:18:42.098] [INFO] normal - 127.0.0.1 - - "GET /js/holder.js HTTP/1.1" 304 - "http://localhost:3000/admin/
" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.36"
GET /js/bootstrap.min.js 304 11ms
[2013-06-19 18:18:42.101] [INFO] normal - 127.0.0.1 - - "GET /js/bootstrap.min.js HTTP/1.1" 304 - "http://localhost:3000
/admin/" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.36"
```

2. 调整日志输出的格式

```{bash}
 app.use(log4js.connectLogger(logger, {level: level:log4js.levels.INFO, format:':method :url'}));
```

输出结果：

```{bash}
[2013-06-19 18:23:25.230] [INFO] console - This is an index page!
GET / 304 28ms
[2013-06-19 18:23:25.251] [INFO] normal - GET /
GET /css/bootstrap.min.css 304 5ms
[2013-06-19 18:23:25.287] [INFO] normal - GET /css/bootstrap.min.css
GET /css/my.css 304 3ms
[2013-06-19 18:23:25.292] [INFO] normal - GET /css/my.css
GET /js/jquery-1.9.1.min.js 304 15ms
[2013-06-19 18:23:25.310] [INFO] normal - GET /js/jquery-1.9.1.min.js
GET /js/holder.js 304 9ms
[2013-06-19 18:23:25.321] [INFO] normal - GET /js/holder.js
GET /js/bootstrap.min.js 304 17ms
[2013-06-19 18:23:25.338] [INFO] normal - GET /js/bootstrap.min.js
```

### 3. 自动调整日志输出级别

日志级别对应规则：
  http responses 3xx, level = WARN
  http responses 4xx & 5xx, level = ERROR
  else, level = INFO

设置level为auto

```{bash}
 app.use(log4js.connectLogger(logger, {level: 'auto', format:':method :url'}));
```

下面日志为了对比方便我多打出了几行。

```{bash}
[2013-06-19 18:24:56.040] [INFO] console - This is an index page!
GET / 304 16ms
[2013-06-19 18:24:56.053] [WARN] normal - GET /
GET /css/bootstrap.min.css 304 9ms
[2013-06-19 18:24:56.086] [WARN] normal - GET /css/bootstrap.min.css
GET /css/my.css 304 9ms
[2013-06-19 18:24:56.097] [WARN] normal - GET /css/my.css
GET /js/jquery-1.9.1.min.js 304 26ms
[2013-06-19 18:24:56.128] [WARN] normal - GET /js/jquery-1.9.1.min.js
GET /js/holder.js 304 32ms
[2013-06-19 18:24:56.164] [WARN] normal - GET /js/holder.js
GET /js/bootstrap.min.js 304 1ms
[2013-06-19 18:24:56.166] [WARN] normal - GET /js/bootstrap.min.js
[2013-06-19 18:24:56.204] [INFO] normal - GET /cat/json/latest
GET /cat/json/latest 200 10ms
[2013-06-19 18:24:56.211] [INFO] normal - GET /cat/json/master
GET /cat/json/master 200 4ms
[2013-06-19 18:24:56.219] [INFO] normal - GET /cat/json/classic
GET /cat/json/classic 200 9ms
GET /img/movie/emptySmall.png 304 1ms
[2013-06-19 18:24:56.263] [WARN] normal - GET /img/movie/emptySmall.png
```

## 5. 优化log4js结构

应该有同学发现了，我们在配置log4js时会有一个问题。就是所有配置信息都是在app.js中做的，logger也是在这里直接定义的。如果在控制器(routes)想用log4js进行输出，我们现在拿不到logger的句柄。

修改app.js，

```{bash}
~ vi app.js

var log4js = require('log4js');
log4js.configure({
  appenders: [
    { type: 'console' },{
      type: 'file', 
      filename: 'logs/access.log', 
      maxLogSize: 1024,
      backups:4,
      category: 'normal' 
    }
  ],
  replaceConsole: true
});
//var logger = log4js.getLogger(name);
//logger.setLevel('INFO');
....
app.use(log4js.connectLogger(this.logger('normal'), {level:'auto', format:':method :url'}));
....

exports.logger=function(name){
  var logger = log4js.getLogger(name);
  logger.setLevel('INFO');
  return logger;
}
```

我们把logger单独定义出来，并且做为API暴露出来。

### 在index.js中使用logger输出

```{bash}
~ vi routes/index.js
var logger = require('../app').logger('index');
exports.index = function(req, res){
	console.log("This is an index page!");
	logger.info("This is an index page! -- log4js");

	res.render('index', {
  		title:'首页|moive.me',
  		page:'index'
  	});
};
```

### 打印出来结果

```{bash}
[2013-06-19 18:56:51.924] [INFO] console - This is an index page!
[2013-06-19 18:56:51.925] [INFO] index - This is an index page! -- log4js
GET / 304 17ms
[2013-06-19 18:56:51.938] [WARN] [default] - GET /
GET /css/bootstrap.min.css 304 5ms
[2013-06-19 18:56:51.978] [WARN] [default] - GET /css/bootstrap.min.css
GET /css/my.css 304 2ms
[2013-06-19 18:56:51.981] [WARN] [default] - GET /css/my.css
GET /js/jquery-1.9.1.min.js 304 2ms
[2013-06-19 18:56:51.984] [WARN] [default] - GET /js/jquery-1.9.1.min.js
GET /js/holder.js 304 3ms
[2013-06-19 18:56:51.989] [WARN] [default] - GET /js/holder.js
GET /js/bootstrap.min.js 304 9ms
[2013-06-19 18:56:52.002] [WARN] [default] - GET /js/bootstrap.min.js
```

这样我们就已经玩转log4js了，为部署到生产环境，做好了日志的准备工作。

#### 转载请注明出处：http://blog.fens.me/nodejs-log4js/






















