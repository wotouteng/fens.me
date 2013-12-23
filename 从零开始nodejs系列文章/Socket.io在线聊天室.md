Socket.io在线聊天室
===========

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-socketio-chat/

![Socket.io在线聊天室](http://blog.fens.me/wp-content/uploads/2013/08/socketio.png)

#### 前言

网络聊天室在web1.0的时代就出现了，但当时技术支持比较有限，大都是通过浏览器插件BHO，JavaApplet，Flash实现的。如今HTML5技术风起云涌，通过websocket实现的网络聊天室，被定义为websocket的第一个实验，就像Hello World一样的简单。

今天我也动手完成了这个实验，感觉真的很爽！

#### 目录

1. socket.io介绍
2. 服务器端和客户端通信设计
3. 服务器端实现
4. 客户端实现
5. 完整案例代码

## 1. socket.io介绍

socket.io一个是基于Nodejs架构体系的，支持websocket的协议用于时时通信的一个软件包。socket.io 给跨浏览器构建实时应用提供了完整的封装，socket.io完全由javascript实现。

基于Nodejs实现webscoket其他的框架，请参考文章：[Nodejs实现websocket的4种方式](http://blog.fens.me/nodejs-websocket/)

## 2. 服务器端和客户端通信设计

![](http://blog.fens.me/wp-content/uploads/2013/08/chat.png)

上图中client1 和 server 描述通信过程，client2描述对其他的客户端，通过广播进行消息通信。

1. client1向server发起连接请求
2. server接受client的连接
3. client1输入登陆用户名
4. server返回欢迎语
5. server通过广播告诉其他在线的用户，client1已登陆
6. client1发送聊天信息
7. server返回聊天信息(可省略)
8. server通过广播告诉其他在线的用户，client1的聊天消息
9. client1关闭连接，退出登陆
10. server通过广播告诉其他在线的用户，client1已退出

### 我们看一下测试截图：

![](http://blog.fens.me/wp-content/uploads/2013/08/chat2.png)

1. 左边: aaa 登陆
2. 右边: bbb 登陆
3. 左边: aaa 收到 bbb登陆欢迎消息
4. aaa 和 bbb 实现对话
5. 右边: bbb 刷新浏览自动退出
6. 左边: aaa 收到 bbb的退出消息
7. 右边: CCC 登陆
8. 左边: aaa 收到 CCC 登陆欢迎消息
9. aaa 和 CCC 实现对话

## 3. 服务器端实现

我们这里使用socket.io和express3的混合模式，让HTTP请求和WebSocket请求都使用3000端口。

服务器端实现，只有一个核心文件app.js。

```{bash}

//引入程序包
var express = require('express')
  , path = require('path')
  , app = express()
  , server = require('http').createServer(app)
  , io = require('socket.io').listen(server);

//设置日志级别
io.set('log level', 1); 

//WebSocket连接监听
io.on('connection', function (socket) {
  socket.emit('open');//通知客户端已连接

  // 打印握手信息
  // console.log(socket.handshake);

  // 构造客户端对象
  var client = {
    socket:socket,
    name:false,
    color:getColor()
  }

  // 对message事件的监听
  socket.on('message', function(msg){
    var obj = {time:getTime(),color:client.color};

    // 判断是不是第一次连接，以第一条消息作为用户名
    if(!client.name){
        client.name = msg;
        obj['text']=client.name;
        obj['author']='System';
        obj['type']='welcome';
        console.log(client.name + ' login');

        //返回欢迎语
        socket.emit('system',obj);
        //广播新用户已登陆
        socket.broadcast.emit('system',obj);
     }else{

        //如果不是第一次的连接，正常的聊天消息
        obj['text']=msg;
        obj['author']=client.name;      
        obj['type']='message';
        console.log(client.name + ' say: ' + msg);

        // 返回消息（可以省略）
        socket.emit('message',obj);
        // 广播向其他用户发消息
        socket.broadcast.emit('message',obj);
      }
    });

    //监听出退事件
    socket.on('disconnect', function () {  
      var obj = {
        time:getTime(),
        color:client.color,
        author:'System',
        text:client.name,
        type:'disconnect'
      };

      // 广播用户已退出
      socket.broadcast.emit('system',obj);
      console.log(client.name + 'Disconnect');
    });

});

//express基本配置
app.configure(function(){
  app.set('port', process.env.PORT || 3000);
  app.set('views', __dirname + '/views');
  app.use(express.favicon());
  app.use(express.logger('dev'));
  app.use(express.bodyParser());
  app.use(express.methodOverride());
  app.use(app.router);
  app.use(express.static(path.join(__dirname, 'public')));
});

app.configure('development', function(){
  app.use(express.errorHandler());
});

// 指定webscoket的客户端的html文件
app.get('/', function(req, res){
  res.sendfile('views/chat.html');
});

server.listen(app.get('port'), function(){
  console.log("Express server listening on port " + app.get('port'));
});

var getTime=function(){
  var date = new Date();
  return date.getHours()+":"+date.getMinutes()+":"+date.getSeconds();
}

var getColor=function(){
  var colors = ['aliceblue','antiquewhite','aqua','aquamarine','pink','red','green',
                'orange','blue','blueviolet','brown','burlywood','cadetblue'];
  return colors[Math.round(Math.random() * 10000 % colors.length)];
}
```

## 4. 客户端实现

这里我们需要定义几个文件：chat.html, chat.js, jquery.min.js, main.css

### 1). views/chat.html

```{bash}

<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Socket.io - Simple chat</title>
<link rel="stylesheet" type="text/css" href="css/main.css">
<script src="javascripts/jquery.min.js"></script>
<script src="/socket.io/socket.io.js"></script>
<script src="javascripts/chat.js"></script>
</head>
<body>
<h1>Socket.io - Simple chat room</h1>
<div>
<span id="status">Connecting...</span>
<input type="text" id="input"/>
</div>
<div id="content"></div>
</body>
</html>
```

### 2). public/javascript/chat.js

```{bash}

$(function () {
var content = $('#content');
var status = $('#status');
var input = $('#input');
var myName = false;

//建立websocket连接
socket = io.connect('http://localhost:3000');
//收到server的连接确认
socket.on('open',function(){
status.text('Choose a name:');
});

//监听system事件，判断welcome或者disconnect，打印系统消息信息
socket.on('system',function(json){
var p = '';
if (json.type === 'welcome'){
if(myName==json.text) status.text(myName + ': ').css('color', json.color);
p = '<p style="background:'+json.color+'">system @ '+ json.time+ ' : Welcome ' + json.text +'</p>';
}else if(json.type == 'disconnect'){
p = '<p style="background:'+json.color+'">system @ '+ json.time+ ' : Bye ' + json.text +'</p>';
}
content.prepend(p);
});

//监听message事件，打印消息信息
socket.on('message',function(json){
var p = '<p><span style="color:'+json.color+';">' + json.author+'</span> @ '+ json.time+ ' : '+json.text+'</p>';
content.prepend(p);
});

//通过“回车”提交聊天信息
input.keydown(function(e) {
if (e.keyCode === 13) {
var msg = $(this).val();
if (!msg) return;
socket.send(msg);
$(this).val('');
if (myName === false) {
myName = msg;
}
}
});
});
```

### 3). public/javascript/jquery.min.js

从jquery官方下载: http://jquery.com/

### 4). public/css/main.css

```{bash}

* {padding:0px; margin:0px;}
body{font-family:tahoma; font-size:12px;margin:10px;}
p {line-height:18px;padding:2px;}
div {width:500px;}
#content { 
    padding:5px; 
    background:#ddd; 
    border-radius:5px;
    border:1px solid #CCC; 
    margin-top:10px; 
}
#input { 
    border-radius:2px; 
    border:1px solid #ccc;
    margin-top:10px; 
    padding:5px; 
    width:380px;  
}
#status { 
    width:100px; 
    display:block; 
    float:left; 
    margin-top:15px; 
}
```

## 5. 完整案例代码

项目已经上传到github: https://github.com/bsspirit/chat-websocket

### 下载，安装，启动服务器

```{bash}

git clone https://github.com/bsspirit/chat-websocket.git
npm install
node app.js
```

### 打开浏览器

可以多打开几个窗口，模拟不同用户有的对话。

http://localhost:3000

做完实验，感觉棒极了。技术创新的革命！！

#### 转载请注明出处：http://blog.fens.me/nodejs-socketio-chat/
