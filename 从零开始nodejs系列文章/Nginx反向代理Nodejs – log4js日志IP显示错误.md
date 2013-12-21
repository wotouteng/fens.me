Nginx反向代理Nodejs – log4js日志IP显示错误
==============

#### 从零开始nodejs系列文章

从零开始nodejs系列文章，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-crawler-douban/

#### 前言

为提高web应用的吞吐量，我们一般都用nginx做负载均衡器，再反向代理到真正的web服务器。像JAVA服务可以做成Nginx+Tomcat, PHP用Nginx+Spawn-fcgi, 当然nodejs服务，我们也可以做相同的架构Ngnix+Node。

我使用nginx+nodejs+log4js时，发现log4js日志的remote_addr项，总是显示127.0.0.1。本文将重点解释如何通过修改log4js的代码，解决这个问题。

我已经提交这个问题到log4js-node的issue
https://github.com/nomiddlename/log4js-node/issues/139

![Nginx反向代理Nodejs – log4js日志IP显示错误](http://blog.fens.me/wp-content/uploads/2013/06/log4js-bug.png)

#### 文章目录：

1. 配置nginx反向代理
2. log4js做为web日志输出
3. log4js日志ip显示错误及修复

## 1. 配置nginx反向代理

本地启动nodejs : node app.js ，默认3000端口。

安装nginx

```{bash}
~ sudo apt-get install nginx
```

进入nginx配置目录

```{bash}
~ cd /etc/nginx/sites-enabled
/etc/nginx/sites-enabled
```

新建一个配置文件moive

```{bash}
~ vi moive

#moive.me
upstream nodejs__upstream {
        server 127.0.0.1:3000;
        #server 127.0.0.1:3001;
        keepalive 64;
}
server {
        listen 80;
        server_name www.moive.me moive.me;
        access_log /var/log/nginx/moiveme.log;
        location / {
                proxy_set_header   X-Real-IP            $remote_addr;
                proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
                proxy_set_header   Host                   $http_host;
                proxy_set_header   X-NginX-Proxy    true;
                proxy_set_header   Connection "";
                proxy_http_version 1.1;
                proxy_pass         http://nodejs__upstream;
        }
}
```

### 对配置项的解释：

+ listen，监听端口80
+ server_name , 监听域名 www.moive.me moive.me
+ access_log ，nginx日志输出 /var/log/nginx/moiveme.log
+ proxy_pass ，反向代理转发 http://nodejs__upstream;
+ 通过upstream nodejs__upstream 可以配置多台nodejs节点，做负载均衡。
+ keepalive ，设置存活时间。如果不设置可能会产生大量的timewait。

重新启动nginx

```{bash}
~ sudo /etc/init.d/nginx restart
```

通过浏览器访问web站点

http://moive.me

## 2. log4js做为web日志输出

log4js的配置与使用，请查看 [玩转Nodejs日志管理log4js](http://blog.fens.me/nodejs-log4js/)

### 查看控制台日志，发现一个问题，客户端IP记录值都是127.0.0.1。这个是我们不期待的。

```{bash}
GET / 304 24ms
[2013-06-20 06:55:39.405] [WARN] [default] - 127.0.0.1 - - "GET / HTTP/1.1" 304 - "" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.36"
GET /css/my.css 304 5ms
[2013-06-20 06:55:39.646] [WARN] [default] - 127.0.0.1 - - "GET /css/my.css HTTP/1.1" 304 - "http://moive.me/" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.36"
GET /css/bootstrap.min.css 304 4ms
[2013-06-20 06:55:39.651] [WARN] [default] - 127.0.0.1 - - "GET /css/bootstrap.min.css HTTP/1.1" 304 - "http://moive.me/" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.36"
GET /js/holder.js 304 3ms
[2013-06-20 06:55:39.652] [WARN] [default] - 127.0.0.1 - - "GET /js/holder.js HTTP/1.1" 304 - "http://moive.me/" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.36"
GET /js/jquery-1.9.1.min.js 304 4ms
[2013-06-20 06:55:39.653] [WARN] [default] - 127.0.0.1 - - "GET /js/jquery-1.9.1.min.js HTTP/1.1" 304 - "http://moive.me/" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.36"
```

### 在浏览器通过IP直接访问，http://50.116.27.194:3000/， 所有的客户端IP显示正常。

```{bash}
GET / 304 12ms
[2013-06-20 06:57:49.492] [WARN] [default] - 114.252.83.33 - - "GET / HTTP/1.1" 304 - "" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.36"
GET /js/holder.js 304 2ms
[2013-06-20 06:57:49.720] [WARN] [default] - 114.252.83.33 - - "GET /js/holder.js HTTP/1.1" 304 - "http://50.116.27.194:3000/" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.36"
GET /css/bootstrap.min.css 304 2ms
[2013-06-20 06:57:49.723] [WARN] [default] - 114.252.83.33 - - "GET /css/bootstrap.min.css HTTP/1.1" 304 - "http://50.116.27.194:3000/" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.36"
GET /css/my.css 304 2ms
[2013-06-20 06:57:49.724] [WARN] [default] - 114.252.83.33 - - "GET /css/my.css HTTP/1.1" 304 - "http://50.116.27.194:3000/" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.36"
```

### 我们把问题定位在nginx转发出现的。

在程序中打印客户端IP。

```{bash}
~ vi routes/index.js

        console.log("ip=>"+req.ip);
	console.log("REMOTE_ADDR=>"+req.get['REMOTE_ADDR']);
	console.log("HTTP_X_REAL_IP=>"+req.get['HTTP_X_REAL_IP']);
	console.log("x-real-ip=>"+req.get['x-real-ip']);
	console.log("x-forwarded-for=>"+req.get['x-forwarded-for']);

	var headers = req.headers;
	console.log("headers=>"+ (headers['x-real-ip'] || headers['x-forwarded-for']));
	console.log("headers x-real-ip=>"+headers['x-real-ip']);
	console.log("headers x-forwarded-for=>"+headers['x-forwarded-for']);

	console.log("====================================================");

	var proxy_ip=headers['x-real-ip'] || headers['x-forwarded-for']
	if(proxy_ip) return console.log("proxy"+proxy_ip);
	if(req.ip) return console.log(req.ip);

	console.log("====================================================");
```

### http://moive.me, 通过nginx转发的输出结果：

```{bash}
[2013-06-20 07:01:48.731] [INFO] console - ip=>127.0.0.1
[2013-06-20 07:01:48.732] [INFO] console - REMOTE_ADDR=>undefined
[2013-06-20 07:01:48.732] [INFO] console - HTTP_X_REAL_IP=>undefined
[2013-06-20 07:01:48.732] [INFO] console - x-real-ip=>undefined
[2013-06-20 07:01:48.732] [INFO] console - x-forwarded-for=>undefined
[2013-06-20 07:01:48.732] [INFO] console - headers=>114.252.83.33
[2013-06-20 07:01:48.732] [INFO] console - headers x-real-ip=>114.252.83.33
[2013-06-20 07:01:48.732] [INFO] console - headers x-forwarded-for=>114.252.83.33
[2013-06-20 07:01:48.732] [INFO] console - ====================================================
[2013-06-20 07:01:48.733] [INFO] console - proxy114.252.83.33
[2013-06-20 07:01:48.733] [INFO] console - req.ip127.0.0.1
```

### http://50.116.27.194:3000, 直接通过IP访问的输出结果

```{bash}
[2013-06-20 06:57:49.480] [INFO] console - ip=>114.252.83.33
[2013-06-20 06:57:49.480] [INFO] console - REMOTE_ADDR=>undefined
[2013-06-20 06:57:49.480] [INFO] console - HTTP_X_REAL_IP=>undefined
[2013-06-20 06:57:49.481] [INFO] console - x-real-ip=>undefined
[2013-06-20 06:57:49.481] [INFO] console - x-forwarded-for=>undefined
[2013-06-20 06:57:49.481] [INFO] console - headers=>undefined
[2013-06-20 06:57:49.481] [INFO] console - headers x-real-ip=>undefined
[2013-06-20 06:57:49.481] [INFO] console - headers x-forwarded-for=>undefined
[2013-06-20 06:57:49.481] [INFO] console - ====================================================
[2013-06-20 06:57:49.482] [INFO] console - req.ip114.252.83.33
```

我们已经发现，通过nginx转发的客户端IP是不能直接使用express3框架中req.ip获得的，而是需要通过req.headers['x-real-ip']和req.headers['x-forwarded-for']才能得到。

## 3. log4js日志ip显示错误及修复

下面我们要再查一下log4js代码，看看他是怎么实现的，为什么会出现IP的错误。

打开 log4js/lib/connect-logger.js 118行

```{bash}
~ vi connect-logger.js
 function format(str, req, res) {
        return str
        .replace(':url', req.originalUrl)
        .replace(':method', req.method)
        .replace(':status', res.__statusCode || res.statusCode)
        .replace(':response-time', res.responseTime)
        .replace(':date', new Date().toUTCString())
        .replace(':referrer', req.headers['referer'] || req.headers['referrer'] || '')
        .replace(':http-version', req.httpVersionMajor + '.' + req.httpVersionMinor)
        .replace(':remote-addr',req.socket && (req.socket.remoteAddress || (req.socket.socket && req.socket.socket.remoteAddress)))
        .replace(':user-agent', req.headers['user-agent'] || '')
        .replace(':content-length', (res._headers && res._headers['content-length']) || (res.__headers && res.__headers['Content-Length']) || '-')
        .replace(/:req\[([^\]]+)\]/g, function(_, field){ return req.headers[field.toLowerCase()]; })
        .replace(/:res\[([^\]]+)\]/g, function(_, field){
                return res._headers
                ? (res._headers[field.toLowerCase()] || res.__headers[field])
                : (res.__headers && res.__headers[field]);
        });
 }
```

我们发现他的代码和express3的req.ip实现的代码是类似的，这样是不识别通过代理程序转发的客户端IP。

```{bash}
.replace(':remote-addr',req.socket && (req.socket.remoteAddress || (req.socket.socket && req.socket.socket.remoteAddress)))
```

### 下面对这行代码进行修改。

```{bash}
.replace(':remote-addr', req.headers['x-forwarded-for'] || req.connection.remoteAddress)
```

先检查不是转发的IP，再取默认IP。

重起nodejs服务器

### 浏览器访问测试。

+ http://moive.me
+ http://50.116.27.194:3000

```{bash}
[2013-06-20 07:12:58.706] [WARN] [default] - 114.252.83.33 - - "GET / HTTP/1.1" 304 - "http://moive.me/" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.36"
GET /css/bootstrap.min.css 304 5ms
[2013-06-20 07:12:58.949] [WARN] [default] - 114.252.83.33 - - "GET /css/bootstrap.min.css HTTP/1.1" 304 - "http://moive.me/" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.36"
GET /css/my.css 304 3ms
[2013-06-20 07:12:59.153] [WARN] [default] - 114.252.83.33 - - "GET /css/my.css HTTP/1.1" 304 - "http://moive.me/" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.36"
GET /js/jquery-1.9.1.min.js 304 2ms
[2013-06-20 07:12:59.156] [WARN] [default] - 114.252.83.33 - - "GET /js/jquery-1.9.1.min.js HTTP/1.1" 304 - "http://moive.me/" "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.110 Safari/537.36"
```

两种访问测试都正常，解决了log4js通过nginx反向代理，日志IP显示错误的问题。

#### 转载请注明出处：http://blog.fens.me/nodejs-nginx-log4js/



