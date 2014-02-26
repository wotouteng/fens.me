快速搭建Web环境 Angularjs + Express3 + Bootstrap3
==========

[AngularJS体验式编程系列文章](http://blog.fens.me/series-angular/)，将介绍如何用angularjs构建一个强大的web前端系统。angularjs是由Google团队开发的一款非常优秀web前端框架。在当前如此多的web框架下，angularjs能脱颖而出，从架构设计上就高人一等，双向数据绑定，依赖注入，指令，MVC，模板。Angular.js创新地把后台技术融入前端开发，扫去jQuery一度的光芒。用angularjs就像写后台代码，更规范，更结构化，更可控。

#### 关于作者

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/angularjs-express3-bootstrap3/

![快速搭建Web环境 Angularjs + Express3 + Bootstrap3](http://blog.fens.me/wp-content/uploads/2014/02/angular-basic.png)

#### 前言

Angularjs越用越顺手，不仅代码量比jQuery少很多，而且实现思路特别清晰，构建大型的Web前端项目，真是最适合不过了。

Bootstrap让界面美观大方，就连像我这种不懂UE的人，也能做出专业级的水准。再结合Nodejs的Express做后端，三剑合并，太无敌了，大有统一前端开发的趋势，前途不可估量！

#### 目录

1. 从零开始手工创建Express3项目
2. 新建Angularjs目录及文件
3. 配置bower
4. 配置Angularjs项目
5. 增加Bootstrap
6. 完整的项目

## 1. 从零开始手工创建Express3项目

### 系统环境：

* Win7 64bit 旗舰版
* node v0.10.5
* npm 1.2.19
* bower 1.1.2

本文截图中使用的开发工具是WebStorm，请参考文章：AngularJS最理想开发工具WebStorm

创建express项目

```{bash}
~ D:\workspace\javascript>express -e angular-basic
~ D:\workspace\javascript>cd angular-basic && npm install
```

生成的express项目目录

![](http://blog.fens.me/wp-content/uploads/2014/02/express.png)

修改app.js的配置

* 修改ejs: 文件扩展名ejs为html
* 设置angular: 启动路径为”/”
* 设置angular: 启动文件为app/index.html

```{bash}


~ vi app.js

var express = require('express')
    , path = require('path')
    , ejs = require('ejs')
    , app = express()
    , server = require('http').createServer(app);

app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.engine('.html', ejs.__express);
app.set('view engine', 'html'); //替换文件扩展名ejs为html
app.use(express.favicon());
app.use(express.logger('dev'));
app.use(express.bodyParser());
app.use(express.methodOverride());
app.use(app.router);
app.use(express.static(path.join(__dirname, 'app')));

if (app.get('env') === 'development') {
    app.use(express.errorHandler());
}

// angular启动页
app.get('/', function (req, res) {
    res.sendfile('app/index.html');
});

server.listen(app.get('port'), function () {
    console.log('Express server listening on port ' + app.get('port'));
});

```

如何单独创建Express3的项目，请参考文章：[Nodejs开发框架Express3.0开发手记–从零开始](http://blog.fens.me/nodejs-express3/)

## 2. 新建Angularjs目录及文件

创建Angularjs需要的目录及文件

```{bash}

D:\workspace\javascript\angular-basic>mkdir app
D:\workspace\javascript\angular-basic>mkdir app\scripts
D:\workspace\javascript\angular-basic>mkdir app\scripts\angular
D:\workspace\javascript\angular-basic>mkdir app\styles
D:\workspace\javascript\angular-basic>mkdir app\views
D:\workspace\javascript\angular-basic>mkdir app\views\component
D:\workspace\javascript\angular-basic>mkdir app\views\tpl

D:\workspace\javascript\angular-basic>touch app\index.html
D:\workspace\javascript\angular-basic>touch app\scripts\angular\app.js
D:\workspace\javascript\angular-basic>touch app\scripts\angular\controllers.js
D:\workspace\javascript\angular-basic>touch app\styles\main.css
D:\workspace\javascript\angular-basic>touch app\views\tpl\welcome.html

D:\workspace\javascript\angular-basic>echo "aaaa" > app\index.html
```

创建的Angularjs目录及文件

![](http://blog.fens.me/wp-content/uploads/2014/02/angular.png)

目录解释：

* app目录: Angular项目的根目录
* scripts目录: 存放Javascript脚本目录
* scripts\angular目录: 存放Angular Javascript脚本目录
* styles目录: 存放css的目录
* views目录: 存放html的目录
* views\component目录: 存放html的自定义组件目录
* views\tpl目录: 存放html的目录

文件解释：

* app\index.html: Angular项目的入口文件
* styles\main.css: Angular项目的css文件
* scripts\angular\app.js: Angular项目全局配置文件
* scripts\angular\controllers.js: Angular项目全局控制器文件/li>
* views\tpl\welcome.html: 欢迎页

删除不需要的文件目录

```{bash}
D:\workspace\javascript\angular-basic>rm -rf public
D:\workspace\javascript\angular-basic>rm -rf routes
```

启动node服务器，检查入口页的配置

```{bash}
D:\workspace\javascript\angular-basic>node app.js
Express server listening on port 3000
GET / 200 11ms - 9b
```

![](http://blog.fens.me/wp-content/uploads/2014/02/indexpage.png)

界面显示”aaaa”，说明node启动express，已经指向到app\index.html的页面。

## 3. 配置bower

接下来，我要通过bower来安装Angularjs和Bootstrap，以及其他依赖的前端库。关于bower的详细使用，请参考文章：[bower解决js的依赖管理](http://blog.fens.me/nodejs-bower-intro/)

新建文件:

* .bowerrc: bower的环境设置，用于指定bower的依赖库的存放位置
* bower.json: bower的依赖管理

新建文件: .bowerrc

```{bash}

~ vi .bowerrc

{
    "directory": "app/bower_components"
}
```

新建文件: bower.json

```{bash}
~ vi bower.json

{
    "name": "angular-basic",
    "version": "0.0.1",
    "dependencies": {
        "angular": "~1.2.12-build.2226",
        "angular-route": "~1.2.12-build.2226"
    },
    "devDependencies": {
    }
}
```

运行bower，下载Angular依赖库

```{bash}
D:\workspace\javascript\angular-basic>bower install
bower angular-route#~1.2.12-build.2226           cached git://github.com/angular/bower-angular-route.git#1.2.13-build.2242
bower angular-route#~1.2.12-build.2226         validate 1.2.13-build.2242 against git://github.com/angular/bower-angular-route.git#~1.2.12-build.2226
bower angular#~1.2.12-build.2226                 cached git://github.com/angular/bower-angular.git#1.2.13-build.2242
bower angular#~1.2.12-build.2226               validate 1.2.13-build.2242 against git://github.com/angular/bower-angular.git#~1.2.12-build.2226
bower angular#1.2.13-build.2242+sha.e645f7c      cached git://github.com/angular/bower-angular.git#1.2.13-build.2242
bower angular#1.2.13-build.2242+sha.e645f7c    validate 1.2.13-build.2242 against git://github.com/angular/bower-angular.git#1.2.13-build.2242+sha.e645f7c
bower angular-route#~1.2.12-build.2226          install angular-route#1.2.13-build.2242
bower angular#~1.2.12-build.2226                install angular#1.2.13-build.2242

angular-route#1.2.13-build.2242 app\bower_components\angular-route
└── angular#1.2.13-build.2242

angular#1.2.13-build.2242 app\bower_components\angular
```

下载最新版本的angular和angular-route，类库存放在app/bower_components目录下面。

![](http://blog.fens.me/wp-content/uploads/2014/02/bower-angular.png)

## 4. 配置Angularjs项目

* 修改index.html: 在入口文件，页面模板
* 修改welcome.html: 欢迎页
* 修改app.js: 全局配置
* 修改controller.js: 控制器

修改index.html

```{bash}

<!DOCTYPE html>
<html lang="zh-cn">
<head>
<meta charset="utf-8">
<title>Angular-basic</title>
<meta name="description" content="Copyright http://blog.fens.me">
<link rel="stylesheet" href="styles/main.css">
</head>
<body ng-app="app">

<ul>
<li><a href="http://blog.fens.me/angularjs-express3-bootstrap3/">快速搭建Web环境 Angularjs + Express3 + Bootstrap3</a></li>
<li>http://blog.fens.me/angularjs-express3-bootstrap3</li>
</ul>

<div ng-view></div>

<script src="bower_components/angular/angular.min.js"></script>
<script src="bower_components/angular-route/angular-route.min.js"></script>
<script src="scripts/angular/app.js"></script>
<script src="scripts/angular/controllers.js"></script>

</body>
</html>
```

修改welcome.html

```{bash}
Welcome {{ username }}
```

修改app.js

```{bash}
'use strict';

var app = angular.module('app', ['ngRoute']);

app.config(['$routeProvider', '$locationProvider', function ($routeProvider, $locationProvider) {
    $routeProvider
        .when('/', {templateUrl: '/views/tpl/welcome.html', controller: 'WelcomeCtrl'})
        .otherwise({redirectTo: '/'});
    $locationProvider.html5Mode(true);
}]);
```

修改controller.js

```{bash}
'use strict';

function WelcomeCtrl($scope){
    $scope.username = 'Conan_Z';
}
```

重新启动node，查看Angular项目。

```{bash}
D:\workspace\javascript\angular-basic>node app.js
Express server listening on port 3000
GET / 304 8ms
GET /styles/main.css 304 3ms
GET /scripts/angular/app.js 304 6ms
GET /scripts/angular/controllers.js 304 11ms
GET /bower_components/angular-route/angular-route.min.js 200 18ms - 3.82kb
GET /bower_components/angular/angular.min.js 200 19ms - 98.03kb
GET /views/tpl/welcome.html 304 9ms
GET /bower_components/angular-route/angular-route.min.js.map 200 21ms - 9.61kb
GET /bower_components/angular/angular.min.js.map 200 26ms - 264.16kb
```

界面显示：

![](http://blog.fens.me/wp-content/uploads/2014/02/angular-start.png)

index.html中配置的链接已经显示，同时welcome.html页面中配置的Welcome Conan_Z，也显示出来了。关于路由和模板配置，请参考文章：[AngularJS路由和模板](http://blog.fens.me/angularjs-route-template/)

## 5. 增加Bootstrap

接下来，增加Bootstrap-v3，让界面好看起来。我们还是有bower来管理Bootstrap的依赖。

通过命令行，增加类库，并写入的bower.json文件

```{bash}
D:\workspace\javascript\angular-basic>bower install bootstrap --save
D:\workspace\javascript\angular-basic>bower install angular-bootstrap --save
```

* 修改index.html: 增加css, js的引用
* 修改welcome.html: 增加bootstrap的效果

修改index.html

```{bash}

<!DOCTYPE html>
<html lang="zh-cn">
<head>
<meta charset="utf-8">
<title>Angular-basic</title>
<meta name="description" content="Copyright http://blog.fens.me">
<link rel="stylesheet" href="bower_components/bootstrap/dist/css/bootstrap.min.css">
<link rel="stylesheet" href="styles/main.css">
</head>
<body ng-app="app">

<div class="container">
<h2 class="text-primary">
<a href="http://blog.fens.me/angularjs-express3-bootstarp3/">快速搭建Web环境 Angularjs + Express3 + Bootstarp3</a>
</h2>
<p>http://blog.fens.me/angularjs-express3-bootstarp3</p>

<div class="row">
<div class=".col-lg-12">
<div ng-view></div>
</div>
</div>
</div>

<script src="bower_components/angular/angular.min.js"></script>
<script src="bower_components/bootstrap/dist/js/bootstrap.min.js"></script>
<script src="bower_components/angular-route/angular-route.min.js"></script>
<script src="bower_components/angular-bootstrap/ui-bootstrap-tpls.min.js"></script>
<script src="scripts/angular/app.js"></script>
<script src="scripts/angular/controllers.js"></script>

</body>
</html>

```

修改welcome.html

```{bash}

<hr/>
<form class="form-inline" role="form">
<div class="form-group">
<label>Welcome</label>
<input type="text" class="form-control" ng-model="username" placeholder="Enter email">
</div>
</form>
<p> {{ username }}</p>
```

![](http://blog.fens.me/wp-content/uploads/2014/02/angular-bootstrap.png)

这样就用手动的方式的搭建了：Angularjs + Express3 + Bootstrap3的组合。

## 6. 完整的项目

项目代码已上传的github，项目地址： https://github.com/bsspirit/angular-basic

项目下载及安装

```{bash}
git clone https://github.com/bsspirit/angular-basic
npm install
bower install
```

项目运行

```{bash}
node app.js
```

当然，对于大型的Angular项目，我们可以选择用Yeoman的种子构建，请参考文章：[AngularJS从构建项目开始](http://blog.fens.me/angularjs-yeoman-project/)

但有时Yeoman的项目，更新不够及时，比如bootstrap已到v3了，种子项目还是bootstrap的v2，而且v3不兼容v2。这时也许手动构建自己的项目，才是更好的选择。

#### 转载请注明出处：http://blog.fens.me/angularjs-express3-bootstrap3/

