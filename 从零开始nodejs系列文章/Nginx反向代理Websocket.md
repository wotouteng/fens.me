Nginx反向代理Websocket
===========

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-websocket-nginx/

![Nginx反向代理Websocket](http://blog.fens.me/wp-content/uploads/2014/03/nodejs-nginx-ws.png)

#### 前言

用Nginx给网站做反向代理和负载均衡，是广泛使用的一种Web技术，不仅能够保证后端服务器的隐蔽性，还可以提高网站情况，部署灵活，而且以来源软件实现负载均衡性价比非常高。

不过今天要讲一下，如何用Nginx给Websocket服务器实现反向代理和负载均衡。我也是第一次做这样的尝试，所以仅把功能实现，优化以后再说。

#### 目录

1. 反向代理和负载均衡
2. 创建基于Node的websocket服务器端
3. 创建websocket客户端
4. 用Nginx实现反向代理
5. 用Nginx实现多websocket服务器的负载均衡

## 1. 反向代理和负载均衡

反向代理（Reverse Proxy）方式是指以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个服务器。

负载均衡 （Load Balancing） 负载均衡建立在现有网络结构之上，它提供了一种廉价有效透明的方法扩展网络设备和服务器的带宽、增加吞吐量、加强网络数据处理能力、提高网络的灵活性和可用性。

## 2. 创建基于Node的websocket服务器端

系统环境：

* win7 64bit
* npm 1.2.19
* node v0.10.5
* bower 1.1.2
* nginx 1.5.11
* redis 2.4.6

创建基于Node的websocket服务器端

![](http://blog.fens.me/wp-content/uploads/2014/03/ws1.png)

新建工程

```{bash}
D:\workspace\javascript>mkdir nginx-websocket && cd nginx-websocket
D:\workspace\javascript\nginx-websocket>npm install socket.io
socket.io@0.9.16 node_modules\socket.io
├── base64id@0.1.0
├── policyfile@0.0.4
├── redis@0.7.3
└── socket.io-client@0.9.16 (xmlhttprequest@1.4.2, uglify-js@1.2.5, active-x-obfuscator@0.0.1, ws@0.4.31)
```

服务器端启动文件app1.js

```{bash}

~ vi app1.js

var port = 3100;
var io = require('socket.io').listen(port);

io.sockets.on('connection', function (socket) {
    socket.emit('server', {
        server: 'Server1',
        port: port
    });
});
```

启动程序

```{bash}
node app1.js
```

## 3. 创建websocket客户端

通过bower安装socket.io-client库和jquery库

```{bash}

#下载socket.io-client
D:\workspace\javascript\nginx-websocket>bower install socket.io-client
bower socket.io-client#*        cached git://github.com/LearnBoost/socket.io-client.git#0.9.16
bower socket.io-client#*      validate 0.9.16 against git://github.com/LearnBoost/socket.io-client.git#*
bower socket.io-client#~0.9.16 install socket.io-client#0.9.16

socket.io-client#0.9.16 bower_components\socket.io-client

#下载jquery
D:\workspace\javascript\nginx-websocket>bower install jquery
bower jquery#*                  cached git://github.com/jquery/jquery.git#2.1.0
bower jquery#*                validate 2.1.0 against git://github.com/jquery/jquery.git#*
bower jquery#*                     new version for git://github.com/jquery/jquery.git#*
bower jquery#*                 resolve git://github.com/jquery/jquery.git#*
bower jquery#*                download https://github.com/jquery/jquery/archive/2.1.0.tar.gz
bower jquery#*                progress received 0.3MB of 0.7MB downloaded, 46%
bower jquery#*                progress received 0.3MB of 0.7MB downloaded, 50%
bower jquery#*                progress received 0.4MB of 0.7MB downloaded, 56%
bower jquery#*                progress received 0.4MB of 0.7MB downloaded, 61%
bower jquery#*                progress received 0.5MB of 0.7MB downloaded, 67%
bower jquery#*                progress received 0.5MB of 0.7MB downloaded, 72%
bower jquery#*                progress received 0.5MB of 0.7MB downloaded, 77%
bower jquery#*                progress received 0.6MB of 0.7MB downloaded, 81%
bower jquery#*                progress received 0.6MB of 0.7MB downloaded, 86%
bower jquery#*                progress received 0.6MB of 0.7MB downloaded, 91%
bower jquery#*                progress received 0.6MB of 0.7MB downloaded, 94%
bower jquery#*                progress received 0.7MB of 0.7MB downloaded, 99%
bower jquery#*                 extract archive.tar.gz
bower jquery#*                resolved git://github.com/jquery/jquery.git#2.1.0
bower jquery#~2.1.0            install jquery#2.1.0
jquery#2.1.0 bower_components\jquery
```

创建静态的html文件，作为websocket客户端：client.html

```{bash}

~ vi client.html

<!DOCTYPE html>
<html>
<head>
<title>Websocket Client</title>
<script src="bower_components/socket.io-client/dist/socket.io.min.js"></script>
<script src="bower_components/jquery/dist/jquery.min.js"></script>
</head>
<body>
<h1>Socket.IO Client</h1>
<div id="content">connecting....</div>

<script>
var socket = io.connect('http://localhost:3100');
socket.on('server', function (data) {
console.log(data);
$('#content').html(JSON.stringify(data));
});
</script>

</body>
</html>
```

用浏览器打开client.html，查看websocket通讯情况。

![](http://blog.fens.me/wp-content/uploads/2014/03/websocket1.png)

服务器端和客户端通讯成功。

接下来，我们用Nginx实现Websocket服务器的反向代理。

## 4. 用Nginx实现反向代理

看文官说明Nginx在1.3以后的版本支持websocket反向代理，但我在Nginx1.4版本测试时失败，nginx1.5.11版本测试成功。

![](http://blog.fens.me/wp-content/uploads/2014/03/ws2.png)

操作步骤：

* 1). 下载安装Nginx
* 2). 修改Nginx配置文件，配置反向代理
* 3). 启动Nginx服务器
* 4). 修改app1.js，增加超时的设置
* 5). 修改client.html，修改nginx访问端口
* 6). 启动Node的Websocket服务器
* 7). 浏览器访问测试

### 1). 下载安装Nginx

下载地址：http://nginx.org/en/download.html

### 2). 修改Nginx配置文件，配置反向代理

修改nginx配置文件：nginx.conf

```{bash}

http {

    // ...省略

    map $http_upgrade $connection_upgrade {
        default upgrade;
        ''      close;
    }

    server {

	    listen       3102;  #监听3102 
	    server_name localhost;

        location / {
            proxy_pass http://localhost:3100; #代理3100 

            proxy_set_header X-Real-IP $remote_addr;
	    proxy_set_header Host $host;
	    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

	    proxy_http_version 1.1;
	    proxy_set_header Upgrade $http_upgrade;
	    proxy_set_header Connection "upgrade";
        }
    }

    // ...省略
}
```

* 设置nginx的访问端口：3102
* 设置nginx的代理端口：3100

### 3). 启动Nginx服务器

```{bash}
D:\toolkit\nginx-1.5.11>nginx.exe
```

### 4). 修改app1.js，增加超时的设置

修改启动文件：app1.js

```{bash}

var port = 3100;
var io = require('socket.io').listen(port);

io.enable('browser client minification');  // send minified client
io.enable('browser client etag');          // apply etag caching logic based on version number
io.enable('browser client gzip');          // gzip the file
//io.set('log level', 1);                    // reduce logging

io.set('transports', [ 'websocket','flashsocket','htmlfile','xhr-polling','jsonp-polling']);

io.sockets.on('connection', function (socket) {

    socket.emit('server', {
        server: 'Server1',
        port: port
    });
});
```

### 5). 修改client.html，修改nginx访问端口

```{bash}
<!DOCTYPE html>
<html>
<head>
<title>Websocket Client</title>
<script src="bower_components/socket.io-client/dist/socket.io.min.js"></script>
<script src="bower_components/jquery/dist/jquery.min.js"></script>
</head>
<body>
<h1>Socket.IO Client</h1>
<div id="content">connecting....</div>

<script>
    var socket = io.connect('http://localhost:3102',{ // 访问3102端口
        'connect timeout': 500,
        'reconnect': true,
        'reconnection delay': 500,
        'reopen delay': 500,
        'max reconnection attempts': 10
    });//设置连接超时

    socket.on('server', function (data) {
        console.log(data);
        $('#content').html(JSON.stringify(data));
    });
</script>

</body>
</html>
```

### 6). 启动Node的Websocket服务器

```{bash}
D:\workspace\javascript\nginx-websocket>node app1.js
```

### 7). 浏览器访问测试

打开网页：

![](http://blog.fens.me/wp-content/uploads/2014/03/websocket2.png)

这样就打通了nginx反向代理Websocket。不过遇到一个问题，就是tcp建立连接时特别慢，目前还没有好的解决办法。

## 5. 用Nginx实现多websocket服务器的负载均衡

接下来，我们在Nginx配置负载均衡。

![](http://blog.fens.me/wp-content/uploads/2014/03/ws3.png)

由于基于Socket.io实现的Websocket服务器是单线程的，而访问实例被保存在MemoryStore中。如果我们想实现多个Websocket服务器或多进程调用，那么我们需要把访问实例单独存储，Socket.io集成了通过Redis的NoSQL数据库存储。

socket.io配置说明，可以参考：[Configuring Socket.IO](https://github.com/LearnBoost/Socket.IO/wiki/Configuring-Socket.IO)

操作步骤：

* 1). 下载安装Redis服务器并启动
* 2). 修改app1.js，增加Redis配置
* 3). 新建app2.js，增加Redis配置
* 4). 修改Nginx配置文件，配置app1,app2的负载均衡
* 5). 启动Nginx服务器，启动app1.js, app2.js
* 6). 浏览器访问测试

### 1). 下载安装Redis服务器并启动

Redis的window版本的下载地址：https://github.com/rgl/redis/downloads

启动redis

```{bash}
D:\toolkit\Redis>redis-server.exe
```

### 2). 修改app1.js，增加Redis配置

修改一个app1.js，绑定Redis存储实例

```{bash}

var port = 3100;
var io = require('socket.io').listen(port);

var RedisStore = require('socket.io/lib/stores/redis')
    , redis  = require('socket.io/node_modules/redis')
    , pub    = redis.createClient()
    , sub    = redis.createClient()
    , client = redis.createClient();

io.set('store', new RedisStore({
    redisPub : pub
    , redisSub : sub
    , redisClient : client
}));

io.enable('browser client minification');  // send minified client
io.enable('browser client etag');          // apply etag caching logic based on version number
io.enable('browser client gzip');          // gzip the file
//io.set('log level', 1);                    // reduce logging

io.set('transports', [ 'websocket','flashsocket','htmlfile','xhr-polling','jsonp-polling']);

io.sockets.on('connection', function (socket) {

    socket.emit('server', {
        server: 'Server1',
        port: port
    });
});
```

### 3). 新建app2.js，增加Redis配置

增加一个app2.js，做为第二个websocket服务器

```{bash}

var port = 3101;
var io = require('socket.io').listen(port);

var RedisStore = require('socket.io/lib/stores/redis')
    , redis  = require('socket.io/node_modules/redis')
    , pub    = redis.createClient()
    , sub    = redis.createClient()
    , client = redis.createClient();

io.set('store', new RedisStore({
    redisPub : pub
    , redisSub : sub
    , redisClient : client
}));

io.enable('browser client minification');  // send minified client
io.enable('browser client etag');          // apply etag caching logic based on version number
io.enable('browser client gzip');          // gzip the file
//io.set('log level', 1);                    // reduce logging

io.set('transports', [ 'websocket','flashsocket','htmlfile','xhr-polling','jsonp-polling']);

io.sockets.on('connection', function (socket) {

    socket.emit('server', {
        server: 'Server2',
        port: port
    });
});
```

### 4). 修改Nginx配置文件，配置3100,3101端口的负载均衡

修改Nginx配置文件：nginx.conf

```{bash}

http{
	map $http_upgrade $connection_upgrade {
		default upgrade;
		''      close;
	}

	upstream websocket {
		#ip_hash;
		server localhost:3100;  
		server localhost:3101;
	}

	server {
		listen       3102;
		server_name localhost;

		location / {
			proxy_pass http://websocket;
			
			#proxy_pass http://localhost:3100/;  
			
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header Host $host;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

			proxy_http_version 1.1;
			proxy_set_header Upgrade $http_upgrade;
			proxy_set_header Connection "upgrade";
		}
	}
}
```

### 5). 启动Nginx服务器，启动app1.js, app2.js

```{bash}
D:\toolkit\nginx-1.5.11>nginx.exe
D:\workspace\javascript\nginx-websocket>node app1.js
D:\workspace\javascript\nginx-websocket>node app2.js
```

### 6). 浏览器访问测试

打开2个浏览器页面：

![](http://blog.fens.me/wp-content/uploads/2014/03/websocket3.png)

这样就实现2个Websocket服务器轮训的负载均衡！！不过，建立连接非常慢的问题依然存在，我还没有什么好的办法。希望了解内情的同学，帮助解答！

#### 转载请注明出处：http://blog.fens.me/nodejs-websocket-nginx/


