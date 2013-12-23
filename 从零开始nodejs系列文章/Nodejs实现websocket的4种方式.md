Nodejs实现websocket的4种方式
===========

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-websocket/

![Nodejs实现websocket的4种方式](http://blog.fens.me/wp-content/uploads/2013/08/nodejs-websocket.png)

#### 前言

WebSocket是HTML5开始提供的一种浏览器与服务器间进行全双工通讯的网络技术。在WebSocket API中，浏览器和服务器只需要要做一个握手(handshaking)的动作，然后，浏览器和服务器之间就形成了一条快速通道。两者之间就直接可以数据互相传送。

WebSocket是一个通信的协议，分为服务器和客户端。服务器放在后台，保持与客户端的长连接，完成双方通信的任务。客户端一般都是实现在支持HTML5浏览器核心中，通过提供JavascriptAPI使用网页可以建立websocket连接。Java实现的案例，请参考：[Java现实WebSocket](http://blog.fens.me/java-websocket-intro/)

今天让我们来看看在nodejs中，如何实现websocket的通信。

#### 目录

1. 为什么用Nodejs
2. node-websocket-server:测试失败
3. node-websocket:测试成功
4. faye-websocket-node: 测试成功
5. socket.io: 测试成功
6. 最后总结

## 1. 为什么用Nodejs？

1. 事件驱动，通过闭包很容易实现客户端的生命活期。
2. 不用担心多线程，锁，并行计算的问题
3. V8引擎速度非常快
4. 对于游戏来说，写一遍游戏逻辑代码，前端后端通用。

### 当然Nodejs也有一些缺点：

1. nodejs更新很快，可能会出现版本联兼容
2. nodejs还不算成熟，还没有大制作。
3. nodejs不像其他的服务器，对于不同的连接，不支持进程和线程操作。

在权衡Nodejs给我们带来无限畅快的开发的同时，要考虑到他的不成熟，特别是对于“长连接”的网络通信应用。下面我将分别，测试一下网上几种Nodejs实现websocket的框架。

### 我的系统环境

+ win7 64bit
+ Nodejs:v0.10.5
+ Npm:1.2.19

```{bash}
~ D:\workspace\javascript>node -v
v0.10.5

~ D:\workspace\javascript>npm -v
1.2.19
```

## 2. node-websocket-server: 测试失败

github: https://github.com/miksago/node-websocket-server
node-websocket-server：是基于nodejs底层API实现的，可能产生不兼容的机率是90-100%，现在已经不建议再使用了。我查了代码库，发现已经有两年没有更新了，所以还在准备用node-websocket-server框架的同学，要特别小心了。

我也做了一个实验，一直会报错，贴一下实验代码。

```{bash}
~ D:\workspace\javascript>mkdir nodejs-websocket-server
~ D:\workspace\javascript>cd nodejs-websocket-server
~ D:\workspace\javascript\nodejs-websocket-server>npm install websocket-server
npm http GET https://registry.npmjs.org/websocket-server
npm http 304 https://registry.npmjs.org/websocket-server
npm http GET https://registry.npmjs.org/websocket-server/-/websocket-server-1.4.04.tgz
npm http 200 https://registry.npmjs.org/websocket-server/-/websocket-server-1.4.04.tgz
websocket-server@1.4.04 node_modules\websocket-server

~ vi app.js
var conns = new Array();

var ws = require("websocket-server");
var server = ws.createServer();

server.addListener("connection", function(connection){
  console.log("Connection request on Websocket-Server");
  conns.push(connection);
  connection.addListener('message',function(msg){
        console.log(msg);
        for(var i=0; i<conns.length; i++){
            if(conns[i]!=connection){
                conns[i].send(msg);
            }
        }
    });
});
server.listen(3001);
```

### 客户端连接：

```{bash}
<html>
<body>
<div id="output"></div>
<script>
function checkBrowser(){
if (window.WebSocket){
log("This browser supports WebSocket!");
} else {
log("This browser does not support WebSocket.");
}
}
function setup(){
var wsServer = 'ws://localhost:3001';
var ws = new WebSocket(wsServer);

ws.onopen = function (e) {
log("Connected to WebSocket server.",e);
sendMessage("Conan");
} ;

ws.onclose = function (e) {
log("Disconnected",e);
} ;

ws.onmessage = function(e) {
log("RECEIVED: " + e.data, e);
ws.close();
}

ws.onerror = function (e) {
log('Error occured: ' + e.data,e);
} ;

var sendMessage = function(msg){
ws.send(msg);
log("SEND : "+ msg);
}
}

function log(s,e) {
var output = document.getElementById("output");
var p = document.createElement("p");
p.style.wordWrap = "break-word";
p.style.padding="10px";
p.style.background="#eee";
p.textContent = "LOG : "+s;
output.appendChild(p);
console.log("LOG : "+s, e);
}

checkBrowser();
setup();
</script>
</body>
</html>
```

### 错误提示：

![](http://blog.fens.me/wp-content/uploads/2013/08/ws-error.png)

查了一下原因，node-websocket-server，不支持websocket的draft-10，而chrome 14+浏览器，只支持draft-10的websocket，这样chrome基本都不能用了。我的chrome版本是28.0.1500.95。所以，大家就换个思路吧！

## 3. WebSocket-Node: 测试成功

github: https://github.com/Worlize/WebSocket-Node
WebSocket-Node，是一个简单的库，不仅支持draft-10，还有之前的各种版本。

服务器端配置

```{bash}
~ D:\workspace\javascript>mkdir nodejs-websocket
~ D:\workspace\javascript>cd nodejs-websocket
D:\workspace\javascript\nodejs-websocket>npm install websocket
npm http GET https://registry.npmjs.org/websocket
npm http 304 https://registry.npmjs.org/websocket

> websocket@1.0.8 install D:\workspace\javascript\nodejs-websocket\node_modules\websocket
> node install.js

[websocket v1.0.8] Attempting to compile native extensions.
[websocket v1.0.8]
    Native code compile failed!!
    Please note that this module DOES NOT REQUIRE the native components
    and will still work without them, though not quite as efficiently.

    On Windows, native extensions require Visual Studio and Python.
    On Unix, native extensions require Python, make and a C++ compiler.
    Start npm with --websocket:verbose to show compilation output (if any).
websocket@1.0.8 node_modules\websocket
```

上面提示有错误，我本机已经装了Visual Studio和Python，看来有些模块本地编译不成功了。

增加app.js

```{bash}

~ vi app.js

// http://ejohn.org/blog/ecmascript-5-strict-mode-json-and-more/
"use strict";

// Optional. You will see this name in eg. 'ps' or 'top' command
process.title = 'node-chat';

// Port where we'll run the websocket server
var webSocketsServerPort = 3001;

// websocket and http servers
var webSocketServer = require('websocket').server;
var http = require('http');

/**
 * Global variables
 */
// latest 100 messages
var history = [ ];
// list of currently connected clients (users)
var clients = [ ];

/**
 * Helper function for escaping input strings
 */
function htmlEntities(str) {
    return String(str).replace(/&/g, '&').replace(//g, '>').replace(/"/g, '"');
}

// Array with some colors
var colors = [ 'red', 'green', 'blue', 'magenta', 'purple', 'plum', 'orange' ];
// ... in random order
colors.sort(function(a,b) { return Math.random() > 0.5; } );

/**
 * HTTP server
 */
var server = http.createServer(function(request, response) {
    // Not important for us. We're writing WebSocket server, not HTTP server
});
server.listen(webSocketsServerPort, function() {
    console.log((new Date()) + " Server is listening on port " + webSocketsServerPort);
});

/**
 * WebSocket server
 */
var wsServer = new webSocketServer({
    // WebSocket server is tied to a HTTP server. WebSocket request is just
    // an enhanced HTTP request. For more info http://tools.ietf.org/html/rfc6455#page-6
    httpServer: server
});

// This callback function is called every time someone
// tries to connect to the WebSocket server
wsServer.on('request', function(request) {
    console.log((new Date()) + ' Connection from origin ' + request.origin + '.');

    // accept connection - you should check 'request.origin' to make sure that
    // client is connecting from your website
    // (http://en.wikipedia.org/wiki/Same_origin_policy)
    var connection = request.accept(null, request.origin); 
    // we need to know client index to remove them on 'close' event
    var index = clients.push(connection) - 1;
    var userName = false;
    var userColor = false;

    console.log((new Date()) + ' Connection accepted.');

    // send back chat history
    if (history.length > 0) {
        connection.sendUTF(JSON.stringify( { type: 'history', data: history} ));
    }

    // user sent some message
    connection.on('message', function(message) {
        if (message.type === 'utf8') { // accept only text
            if (userName === false) { // first message sent by user is their name
                // remember user name
                userName = htmlEntities(message.utf8Data);
                // get random color and send it back to the user
                userColor = colors.shift();
                connection.sendUTF(JSON.stringify({ type:'color', data: userColor }));
                console.log((new Date()) + ' User is known as: ' + userName
                            + ' with ' + userColor + ' color.');

            } else { // log and broadcast the message
                console.log((new Date()) + ' Received Message from '
                            + userName + ': ' + message.utf8Data);

                // we want to keep history of all sent messages
                var obj = {
                    time: (new Date()).getTime(),
                    text: htmlEntities(message.utf8Data),
                    author: userName,
                    color: userColor
                };
                history.push(obj);
                history = history.slice(-100);

                // broadcast message to all connected clients
                var json = JSON.stringify({ type:'message', data: obj });
                for (var i=0; i < clients.length; i++) {
                    clients[i].sendUTF(json);
                }
            }
        }
    });

    // user disconnected
    connection.on('close', function(connection) {
        if (userName !== false && userColor !== false) {
            console.log((new Date()) + " Peer "
                + connection.remoteAddress + " disconnected.");
            // remove user from the list of connected clients
            clients.splice(index, 1);
            // push back user's color to be reused by another user
            colors.push(userColor);
        }
    });

});
```

### 启动服务器

```{bash}
~ D:\workspace\javascript\nodejs-websocket>node app.js
Warning: Native modules not compiled.  XOR performance will be degraded.
Warning: Native modules not compiled.  UTF-8 validation disabled.
Wed Aug 21 2013 15:28:48 GMT+0800 (中国标准时间) Server is listening on port 3001
Wed Aug 21 2013 15:28:53 GMT+0800 (中国标准时间) Connection from origin null.
Wed Aug 21 2013 15:28:53 GMT+0800 (中国标准时间) Connection accepted.
Wed Aug 21 2013 15:28:53 GMT+0800 (中国标准时间) User is known as: Conan with red color.
Wed Aug 21 2013 15:28:53 GMT+0800 (中国标准时间) Peer undefined disconnected.
```

我们看到XOR 和UTF-8 validation 两个模块是被禁止的，不过不影响测试。

### 客户端，还是使用一样的。

![](http://blog.fens.me/wp-content/uploads/2013/08/ws-client1.png)

### 另外，WebSocket-Node还提供C/S模式的交互，可以不使用浏览器

服务端代码

```{bash}
~ vi app2.js

#!/usr/bin/env node
var WebSocketServer = require('websocket').server;
var http = require('http');

var server = http.createServer(function(request, response) {
    console.log((new Date()) + ' Received request for ' + request.url);
    response.writeHead(404);
    response.end();
});
server.listen(3001, function() {
    console.log((new Date()) + ' Server is listening on port 3001');
});

wsServer = new WebSocketServer({
    httpServer: server,
    // You should not use autoAcceptConnections for production
    // applications, as it defeats all standard cross-origin protection
    // facilities built into the protocol and the browser.  You should
    // *always* verify the connection's origin and decide whether or not
    // to accept it.
    autoAcceptConnections: false
});

function originIsAllowed(origin) {
  // put logic here to detect whether the specified origin is allowed.
  return true;
}

wsServer.on('request', function(request) {
    if (!originIsAllowed(request.origin)) {
      // Make sure we only accept requests from an allowed origin
      request.reject();
      console.log((new Date()) + ' Connection from origin ' + request.origin + ' rejected.');
      return;
    }

    var connection = request.accept('echo-protocol', request.origin);
    console.log((new Date()) + ' Connection accepted.');
    connection.on('message', function(message) {
        if (message.type === 'utf8') {
            console.log('Received Message: ' + message.utf8Data);
            connection.sendUTF(message.utf8Data);
        }
        else if (message.type === 'binary') {
            console.log('Received Binary Message of ' + message.binaryData.length + ' bytes');
            connection.sendBytes(message.binaryData);
        }
    });
    connection.on('close', function(reasonCode, description) {
        console.log((new Date()) + ' Peer ' + connection.remoteAddress + ' disconnected.');
    });
});
```

客户端代码

```{bash}

~ vi client.js

#!/usr/bin/env node
var WebSocketClient = require('websocket').client;

var client = new WebSocketClient();

client.on('connectFailed', function(error) {
    console.log('Connect Error: ' + error.toString());
});

client.on('connect', function(connection) {
    console.log('WebSocket client connected');
    connection.on('error', function(error) {
        console.log("Connection Error: " + error.toString());
    });
    connection.on('close', function() {
        console.log('echo-protocol Connection Closed');
    });
    connection.on('message', function(message) {
        if (message.type === 'utf8') {
            console.log("Received: '" + message.utf8Data + "'");
        }
    });

    function sendNumber() {
        if (connection.connected) {
            var number = Math.round(Math.random() * 0xFFFFFF);
            connection.sendUTF(number.toString());
            setTimeout(sendNumber, 1000);
        }
    }
    sendNumber();
});

client.connect('ws://localhost:3001/', 'echo-protocol');
```

程序启动：

```{bash}
~ D:\workspace\javascript\nodejs-websocket>node app2.js
~ D:\workspace\javascript\nodejs-websocket>node client.js
```

![](http://blog.fens.me/wp-content/uploads/2013/08/ws-node.png)

测试成功！！

## 4. faye-websocket-node: 测试成功

github: https://github.com/faye/faye-websocket-node
faye-websocket-node，是扩展faye项目而开发的websocket的一个实现。

服务器端配置

```{bash}

~ D:\workspace\javascript>mkdir nodejs-faye-websocket
~ D:\workspace\javascript>cd nodejs-faye-websocket
~ D:\workspace\javascript\nodejs-faye-websocket>npm install faye-websocket
npm http GET https://registry.npmjs.org/faye-websocket
npm http 304 https://registry.npmjs.org/faye-websocket
npm http GET https://registry.npmjs.org/faye-websocket/-/faye-websocket-0.6.1.tgz
npm http 200 https://registry.npmjs.org/faye-websocket/-/faye-websocket-0.6.1.tgz
npm http GET https://registry.npmjs.org/websocket-driver
npm http 200 https://registry.npmjs.org/websocket-driver
npm http GET https://registry.npmjs.org/websocket-driver/-/websocket-driver-0.2.2.tgz
npm http 200 https://registry.npmjs.org/websocket-driver/-/websocket-driver-0.2.2.tgz
faye-websocket@0.6.1 node_modules\faye-websocket
└── websocket-driver@0.2.2

~ vi app.js
var WebSocket = require('faye-websocket'),
    http      = require('http');

var server = http.createServer();

server.on('upgrade', function(request, socket, body) {
  if (WebSocket.isWebSocket(request)) {
    var ws = new WebSocket(request, socket, body);

    ws.on('message', function(event) {
      ws.send(event.data);
    });

    ws.on('close', function(event) {
      console.log('close', event.code, event.reason);
      ws = null;
    });
  }
});

server.listen(3001);

~ D:\workspace\javascript\nodejs-faye-websocket>node app.js
```

用网页客户端访问：

![](http://blog.fens.me/wp-content/uploads/2013/08/ws-faye.png)

测试成功，非常简单！！而且，没有依赖其他的库！！

## 5. socket.io: 测试成功

github: https://github.com/LearnBoost/socket.io

环境配置

```{bash}
~ D:\workspace\javascript>express -e nodejs-socketio
~ D:\workspace\javascript>cd nodejs-socketio && npm install
~ D:\workspace\javascript\nodejs-socketio>npm install socket.io
```

修改app.js配置文件

```{bash}

~ vi app.js

var app = require('express')()
  , server = require('http').createServer(app)
  , io = require('socket.io').listen(server);

server.listen(80);

app.get('/', function (req, res) {
  res.sendfile(__dirname + '/client/index.html');
});

io.sockets.on('connection', function (socket) {
  socket.emit('news', { hello: 'world' });
  socket.on('my other event', function (data) {
    console.log(data);
  });
});
```

增加客户端文件，注意这个的index.html要根据app.js指定的位置”res.sendfile(__dirname + ‘/client/index.html’);”

```{bash}

~ mkdir client
~ vi /client/index.html

<!DOCTYPE html>
<html>
<head>
<title>socket.io</title>
<link rel='stylesheet' href='/stylesheets/style.css' />
</head>
<body>
<h1>socket.io</h1>
<p>Welcome to socket.io</p>

<script src="/socket.io/socket.io.js"></script>
<script>
var socket = io.connect('http://localhost');
socket.on('news', function (data) {
console.log(data);
socket.emit('my other event', { my: 'data' });
});
</script>
</body>
</html>
```

### 启动服务器

```{bash}

~ D:\workspace\javascript\nodejs-socketio>node app.js
   info  - socket.io started
```

打开浏览器: http://localhost

![](http://blog.fens.me/wp-content/uploads/2013/08/ws-socketio.png)

### 查看服务器日志：

```{bash}
  debug - served static content /socket.io.js
   debug - client authorized
   info  - handshake authorized ZR-xQhsKCCqM03TRHW4b
   debug - setting request GET /socket.io/1/websocket/ZR-xQhsKCCqM03TRHW4b
   debug - set heartbeat interval for client ZR-xQhsKCCqM03TRHW4b
   debug - client authorized for
   debug - websocket writing 1::
   debug - websocket writing 5:::{"name":"news","args":[{"hello":"world"}]}
{ my: 'data' }
   debug - emitting heartbeat for client ZR-xQhsKCCqM03TRHW4b
   debug - websocket writing 2::
   debug - set heartbeat timeout for client ZR-xQhsKCCqM03TRHW4b
   debug - got heartbeat packet
   debug - cleared heartbeat timeout for client ZR-xQhsKCCqM03TRHW4b
   debug - set heartbeat interval for client ZR-xQhsKCCqM03TRHW4b
```

测试成功。

## 6. 最后总结

今天尝试了4种，基于nodejs的websocket的框架。

+ node-websocket-server：是直接放弃的。
+ node-websocket：需要依赖于底层的C++,Python的环境，支持以node做客户端的访问。
+ faye-websocket-node：是faye软件框架体系的一部分，安装简单，不需要其他依赖库。
+ socket.io：功能强大，支持集成websocket服务器端和Express3框架与一身。

websocket我也是初次尝试，对于开发效率，代码结构，稳定性，服务器性能都需要做更多的测试。目前还无法定论，哪个框架是最好的，不过我比较看好socket.io和faye-websocket-node的未来前景。

#### 转载请注明出处：http://blog.fens.me/nodejs-websocket/

