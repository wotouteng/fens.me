ngTable动态更新的三种方式
======

[AngularJS体验式编程系列文章](http://blog.fens.me/series-angular/)，将介绍如何用angularjs构建一个强大的web前端系统。angularjs是由Google团队开发的一款非常优秀web前端框架。在当前如此多的web框架下，angularjs能脱颖而出，从架构设计上就高人一等，双向数据绑定，依赖注入，指令，MVC，模板。Angular.js创新地把后台技术融入前端开发，扫去jQuery一度的光芒。用angularjs就像写后台代码，更规范，更结构化，更可控。

#### 关于作者

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/angularjs-ngtable-update/

![ngTable动态更新的三种方式](http://blog.fens.me/wp-content/uploads/2013/09/ngTable.png)

#### 前言

表格是HTML中常见的表现形式，当我们用AngularJS做前端开发时，我可以选择用ngTable的开源组件。ngTable已经为了我们封装了常用的表格操作，节省我们大量的开发时间。

本文将介绍如何动态更新表格，分为3种方式：1. 前端更新, 2. Ajax更新, 3. websocket更新。

#### 目录

1. 前端更新
2. Ajax更新
3. WebScoket更新

## 1. ngTable介绍

ngTable是一个基于AngularJS的directive设计一个表格项目，支持基本的表格操作，分页，排序，异步加载等功能。

项目地址：http://esvit.github.io/ng-table

开发环境还是基于我们之前用[yeoman构建的项目](http://blog.fens.me/angularjs-yeoman-project/)。

## 2. 前端更新

前端更新，是指在AngularJS中，通过JS计算，更新表格中的数据。

### 增加两个文件：

* ngTable.html: AngularJS启动的APP
* mytable.js: 实现脚本

依赖的JS库类，包括jquery,ng-table，可以自己手动下载配置到项目，也可以通过bower自动安装到项目中, bower的介绍，请参考：[bower解决js的依赖管理](http://blog.fens.me/nodejs-bower-intro/)

### 增加文件：app/ngTable.html

```{bash}

<!doctype html>
<head>
<meta charset="utf-8">
<title>ngTable</title>
<link rel="stylesheet" href="styles/bootstrap.css">
</head>
<body ng-app="myTable">

<div class="span8" ng-controller="table1Ctrl">
<h3>js更新</h3>
<table ng-table class="table">
<tr ng-repeat="user in users">
<td title="Name">{{user.name}}</td>
<td title="Value">{{user.age}}</td>
</tr>
</table>
</div>

<script src="bower_components/jquery/jquery.min.js"></script>
<script src="bower_components/angular/angular.min.js"></script>
<script src="bower_components/ng-table/ng-table.src.js"></script>
<script src="scripts/demo/mytable.js"></script>
</body>
</html>
```

### 增加文件：scripts/demo/mytable.js

```{bash}
'use strict';

var myTableApp = angular.module('myTable', ['ngTable']);
function table1Ctrl($scope){
    $scope.users = [
        {name: "Moroni", age: 0},
        {name: "Enos", age: 0}
    ];

    setInterval(function(){
        $scope.users = [
            {name: "Moroni", age: Math.random()*100},
            {name: "Enos", age: Math.random()*100}
        ];
        $scope.$apply();
        console.log($scope.users[0]);
    },2000);
};
```

### 启动程序：

```{bash}
grunt server
```

### 打开浏览器

![](http://blog.fens.me/wp-content/uploads/2013/09/ngTable1.png)

我们实现了，在AngularJS中，对ngTable的动态更新。

3. Ajax更新

Ajax更新，是指通过Ajax取数据，更新表格中的内容。

### 在刚才2个文件上面增加内容：

* ngTable.html: AngularJS启动的APP
* mytable.js: 实现脚本
* ngTable.json：JSON数据文件

### 编辑文件：app/ngTable.html

新加新表格: ng-controller=”table2Ctrl”

```{bash}

<div class="span8" ng-controller="table2Ctrl">
<h3>Ajax更新</h3>
<table ng-table class="table">
<tr ng-repeat="user in users">
<td title="Name">{{user.name}}</td>
<td title="Value">{{user.age}}</td>
</tr>
</table>
</div>
```

### 编辑文件：scripts/demo/mytable.js

增加新controller: table2Ctrl

```{bash}
function table2Ctrl($scope,$http,$timeout){
    $scope.users = [
        {name: "Moroni", age: 0},
        {name: "Enos", age: 0}
    ];

    $timeout(function(){
        $http.get("/json/ngTable.json").success(function(data, status, headers, config){
            $scope.users = data;
        });
        console.log("AJAX loading delay 3s.");
    }, 3000);
}
```

### 增加文件：json/ngTable.json

```{bash}
[
    {"name": "Moroni", "age": 10},
    {"name": "Enos", "age": 15}
]
```

打开浏览器，看到效果

![](http://blog.fens.me/wp-content/uploads/2013/09/ngTable2.png)

通过Ajax，等待3秒后，实现对ngTable的更新。

## 4. WebScoket更新

WebScoket更新，是指通过WebScoket协议由服务器端主动PUSH，更新ngTable表格中的内容。

通过WebSocket操作，会稍微有点复杂，我们需要调整一下应用的结构。

```{bash}
AngularJS(Socket.io.client) + Nodejs(Express3) + WebSocket(Socket.io)
```

安装express3, ejs, socket.io的依赖包

```{bash}
npm install express3
npm install ejs
npm install socket.io
```

当然，再好一点的方法是，在package.json文件中，增加依赖关系再安装

```{bash}
~ vi package.json

//部分代码
"dependencies": {
    "express":"~3.2.6",
    "ejs":"0.8.4",
    "socket.io": ">=0.9.16"
    ...
}

~ npm install
```

### 需要修改3个文件：

* ngTableApp.js: nodejs的启动文件
* ngTable.html: AngularJS启动的APP
* mytable.js: 实现脚本

增加文件：ngTableApp.js

```{bash}

~ vi ngTableApp.js

var express = require('express')
    , path = require('path')
    , ejs = require('ejs')
    , app = express()
    , server = require('http').createServer(app)
    , io = require('socket.io').listen(server);

io.on('connection', function (socket) {
    console.log(socket.handshake);
    setInterval(function(){
        var users= [
                {"name": "Moroni", "age": Math.round(Math.random() * 100)},
                {"name": "Enos", "age":Math.round(Math.random() * 100)}
        ];
        socket.emit('ngTableSocket', users);
    },2000);
});

app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.engine('.html', ejs.__express);
app.set('view engine', 'html');
app.use(express.favicon());
app.use(express.logger('dev'));
app.use(express.bodyParser());
app.use(express.methodOverride());
app.use(app.router);
app.use(express.static(path.join(__dirname, 'app')));

if (app.get('env') === 'development') {
    app.use(express.errorHandler());
};

if (app.get('env') === 'production') {
    // TODO
};

app.get('/', function(req, res){
    res.sendfile('views/ngTable.html');
});

server.listen(app.get('port'), function () {
    console.log('Express server listening on port ' + app.get('port'));
});
```

增加文件：views/ngTable.html

```{bash}
<!doctype html>
<head>
<meta charset="utf-8">
<title>ngTable</title>
<link rel="stylesheet" href="styles/bootstrap.css">
</head>
<body ng-app="myTable">

<div class="span8" ng-controller="table3Ctrl">
<h3>WebSocket更新</h3>
<table ng-table class="table">
<tr ng-repeat="user in users">
<td title="Name">{{user.name}}</td>
<td title="Value">{{user.age}}</td>
</tr>
</table>
</div>

<script src="bower_components/jquery/jquery.min.js"></script>
<script src="bower_components/angular/angular.min.js"></script>
<script src="bower_components/ng-table/ng-table.src.js"></script>
<script src="bower_components/socket.io-client/dist/socket.io.js"></script>
<script src="scripts/demo/mytable.js"></script>
</body>
</html>
```

### 编辑文件：scripts/demo/mytable.js

```{bash}
var socket = io.connect("localhost");
function table3Ctrl($scope){
    $scope.users = [
        {name: "Moroni", age: 0},
        {name: "Enos", age: 0}
    ];

    socket.on('ngTableSocket', function (data) {
        $scope.users = data;
        $scope.$apply();
        console.log($scope.users[0]);
    });
}
```

启动Node:

```{bash}
node ngTableApp.js
```

打开浏览：http://localhost:3000/

![](http://blog.fens.me/wp-content/uploads/2013/09/ngTable3.png)

最上面的webui, 中间是浏览器的console, 最下的nodejs的日志。

我们顺利地用3种方式完成了，ngTable的动态更新！其它的控件也都是类似的操作，如果开发监控系统，websocket绝对是最优的选择，比起Ajax的性能，好上N多倍。

#### 转载请注明出处：http://blog.fens.me/angularjs-ngtable-update/




