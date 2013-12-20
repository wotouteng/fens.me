Nodejs开发框架Express3.0开发手记–从零开始
=======

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-enviroment/

#### 程序代码已经上传到github有需要的同学，自行下载。
https://github.com/bsspirit/nodejs-demo

![Nodejs开发框架Express3.0开发手记–从零开始](http://blog.fens.me/wp-content/uploads/2013/05/nodejs-1.png)

#### 前言

Nodejs给Javascript赋予了服务端应用的生命，Jquery让Javascript成为浏览中开发的利器。 最近学习了Nodejs的Express3.0的开发框架，本来是按照“node.js开发指南”书中介绍，但“node.js开发指南”讲的是Express2.x的，从Express2.x到Express3.0自己模索中还是走了不少弯路的。写篇文章总结一下。

#### 目录

此文重点介绍Express3.0的开发框架，其中还会涉及Mongoose,Ejs,Bootstrap等相关内容。

1. 建立工程
2. 目录结构
3. Express3.0配置文件
4. Ejs模板使用
5. Bootstrap界面框架
6. 路由功能
7. Session使用
8. 页面提示
9. 页面访问控制

#### 开发环境：

**Win7旗舰版 64bit**

**MonogoDB: v2.4.3**

```{bash}

Tue May 14 09:24:50.118 [initandlisten] MongoDB starting : pid=1716 port=27017 dbpath=./data 64-bit host=PC201304202140
Tue May 14 09:24:50.119 [initandlisten] db version v2.4.3
Tue May 14 09:24:50.119 [initandlisten] git version: fe1743177a5ea03e91e0052fb5e2cb2945f6d95f
Tue May 14 09:24:50.119 [initandlisten] build info: windows sys.getwindowsversion(major=6, minor=1, build=7601, platform=2, service_pack='Service Pack 1') BOOST_LIB_VERSION=1_49
Tue May 14 09:24:50.119 [initandlisten] allocator: system
Tue May 14 09:24:50.119 [initandlisten] options: { dbpath: "./data" }
Tue May 14 09:24:50.188 [initandlisten] journal dir=./data\journal
Tue May 14 09:24:50.189 [initandlisten] recover : no journal files present, no recovery needed
Tue May 14 09:24:50.441 [initandlisten] preallocateIsFaster=true 3.26
Tue May 14 09:24:50.778 [initandlisten] preallocateIsFaster=true 5.88
Tue May 14 09:24:51.827 [initandlisten] waiting for connections on port 27017
Tue May 14 09:24:51.827 [websvr] admin web console waiting for connections on port 28017
```

**nodejs: v0.10.5, npm 1.2.19**

```{bash}
node -v
v0.10.5
npm -v
1.2.19
```

## 1. 建立工程

### 进入工程目录

```{bash}
cd D:\workspace\project
```

### 全局安装express，express作为命令被安装到了系统中

```{bash}
npm install -g express
```

### 查看express版本

```{bash}
express -V
3.2.2
```

### 使用express命令创建工程，并支持ejs

```{bash}

D:\workspace\project>express -e nodejs-demo

create : nodejs-demo
create : nodejs-demo/package.json
create : nodejs-demo/app.js
create : nodejs-demo/public
create : nodejs-demo/public/javascripts
create : nodejs-demo/public/images
create : nodejs-demo/public/stylesheets
create : nodejs-demo/public/stylesheets/style.css
create : nodejs-demo/routes
create : nodejs-demo/routes/index.js
create : nodejs-demo/routes/user.js
create : nodejs-demo/views
create : nodejs-demo/views/index.ejs

install dependencies:
$ cd nodejs-demo && npm install
run the app:
$ node app
```

### 根据提示，下载依赖包

```{bash}
cd nodejs-demo && npm install

express@3.2.2 node_modules\express
├── methods@0.0.1
├── fresh@0.1.0
├── buffer-crc32@0.2.1
├── range-parser@0.0.4
├── cookie-signature@1.0.1
├── cookie@0.0.5
├── qs@0.6.3
├── commander@0.6.1
├── debug@0.7.2
├── mkdirp@0.3.4
├── send@0.1.0 (mime@1.2.6)
└── connect@2.7.8 (pause@0.0.1, bytes@0.2.0, formidable@1.0.13)
```

### 模板项目建立成功，启动模板项目。

```{bash}
D:\workspace\project\nodejs-demo>node app.js
Express server listening on port 3000
```

本地的3000端口被打开，通过浏览器访问: localhost:3000

通过node启动程序，每次代码修改都需要重新启动。 有一个工具supervisor，每次修改代码后会自动重启，会我们开发省很多的时间。

```{bash}
npm install supervisor
```

### 再启动服务

```{bash}
D:\workspace\project\nodejs-demo>supervisor app.js

DEBUG: Running node-supervisor with
DEBUG: program 'app.js'
DEBUG: --watch '.'
DEBUG: --ignore 'undefined'
DEBUG: --extensions 'node|js'
DEBUG: --exec 'node'

DEBUG: Starting child process with 'node app.js'
DEBUG: Watching directory 'D:\workspace\project\nodejs-demo' for changes.
Express server listening on port 3000
```

## 2. 目录结构

D:\workspace\project\nodejs-demo>dir

+ 2013/05/14 09:42 877 app.js
+ 2013/05/14 09:48 <DIR> node_modules
+ 2013/05/14 09:42 184 package.json
+ 2013/05/14 09:42 <DIR> public
+ 2013/05/14 09:42 <DIR> routes
+ 2013/05/14 09:42 <DIR> views

### 目录介绍：

+ node_modules, 存放所有的项目依赖库。(每个项目管理自己的依赖，与Maven,Gradle等不同)
+ package.json，项目依赖配置及开发者信息
+ app.js，程序启动文件
+ public，静态文件(css,js,img)
+ routes，路由文件(MVC中的C,controller)
+ Views，页面文件(Ejs模板)

## 3. Express3.0配置文件

### 打开app.js文件

```{bash}

/**
* 模块依赖
*/
var express = require('express')
, routes = require('./routes')
, user = require('./routes/user')
, http = require('http')
, path = require('path');

var app = express();

//环境变量
app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.set('view engine', 'ejs');
app.use(express.favicon());
app.use(express.logger('dev'));
app.use(express.bodyParser());
app.use(express.methodOverride());
app.use(app.router);
app.use(express.static(path.join(__dirname, 'public')));

// 开发模式
if ('development' == app.get('env')) {
app.use(express.errorHandler());
}

// 路径解析
app.get('/', routes.index);
app.get('/users', user.list);

// 启动及端口
http.createServer(app).listen(app.get('port'), function(){
console.log('Express server listening on port ' + app.get('port'));
});
```

## 4. Ejs模板使用

让ejs模板文件，使用扩展名为html的文件。

### 修改：app.js

```{bash}
app.engine('.html', ejs.__express);
app.set('view engine', 'html');// app.set('view engine', 'ejs');
```

### 修改后，ejs变量没有定义，supervisor的程序会一直报错

```{bash}
ReferenceError: ejs is not defined
at Object. (D:\workspace\project\nodejs-demo\app.js:17:21)
at Module._compile (module.js:456:26)
at Object.Module._extensions..js (module.js:474:10)
at Module.load (module.js:356:32)
at Function.Module._load (module.js:312:12)
at Function.Module.runMain (module.js:497:10)
at startup (node.js:119:16)
at node.js:901:3
DEBUG: Program node app.js exited with code 8
```

### 在app.js中增加ejs变量

```{bash}
var express = require('express')
, routes = require('./routes')
, user = require('./routes/user')
, http = require('http')
, path = require('path')
, ejs = require('ejs');
```

### 访问localhost:3000，程序报错

```{bash}
Error: Failed to lookup view "index"
at Function.app.render (D:\workspace\project\nodejs-demo\node_modules\express\lib\application.js:495:17)
at ServerResponse.res.render (D:\workspace\project\nodejs-demo\node_modules\express\lib\response.js:756:7)
at exports.index (D:\workspace\project\nodejs-demo\routes\index.js:7:7)
at callbacks (D:\workspace\project\nodejs-demo\node_modules\express\lib\router\index.js:161:37)
at param (D:\workspace\project\nodejs-demo\node_modules\express\lib\router\index.js:135:11)
at pass (D:\workspace\project\nodejs-demo\node_modules\express\lib\router\index.js:142:5)
at Router._dispatch (D:\workspace\project\nodejs-demo\node_modules\express\lib\router\index.js:170:5)
at Object.router (D:\workspace\project\nodejs-demo\node_modules\express\lib\router\index.js:33:10)
at next (D:\workspace\project\nodejs-demo\node_modules\express\node_modules\connect\lib\proto.js:190:15)
at Object.methodOverride [as handle] (D:\workspace\project\nodejs-demo\node_modules\express\node_modules\connect\lib\middleware\methodOverride.js:37:5)
GET / 500 26ms
```

### 重命名：views/indes.ejs 为 views/index.html

访问localhost:3000正确

## 5. 增加Bootstrap界面框架

其实就是把js,css文件复制到项目中对应该的目录里。 包括4个文件：

复制到public/stylesheets目录

```{bash}
bootstrap.min.css
bootstrap-responsive.min.css
```

复制到public/javascripts目录

```{bash}
bootstrap.min.js
jquery-1.9.1.min.js
```

接下来，我们把index.html页面切分成3个部分：header.html, index.html, footer.html

+ header.html, 为html页面的头部区域
+ index.html, 为内容显示区域
+ footer.html，为页面底部区域

### header.html

```{bash}

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title><%=: title %></title>
<!-- Bootstrap -->
<link href="/stylesheets/bootstrap.min.css" rel="stylesheet" media="screen">
<!-- <link href="css/bootstrap-responsive.min.css" rel="stylesheet" media="screen"> -->
</head>
<body screen_capture_injected="true">
```

### index.html

```{bash}
<% include header.html %>
<h1><%= title %></h1>
<p>Welcome to <%= title %></p>
<% include footer.html %>
```

注：express3.0时，ejs嵌入其他页面时使用include，express2.x用法不一样。

### footer.html

```{bash}
<script src="/javascripts/jquery-1.9.1.min.js"></script>
<script src="/javascripts/bootstrap.min.js"></script>
</body>
</html>
```

访问localhost:3000正确。

我们已经成功的使用了EJS模板的功能，把公共的头部和底部从页面中分离出来了。

并已经引入了bootstrap界面框架，后面讲到“登陆界面”的时候，就会看到bootstrap界面效果了。

## 6. 路由功能

我们设计一下用户登陆业务需求。

+ 访问路径：/，页面：index.html，不需要登陆，可以直接访问。
+ 访问路径：/home，页面：home.html，必须用户登陆后，才可以访问。
+ 访问路径：/login，页面：login.html，登陆页面，用户名密码输入正确，自动跳转到home.html
+ 访问路径：/logout，页面：无，退出登陆后，自动回到index.html页面
+ 打开app.js文件，在增加路由配置

```{bash}
app.get('/', routes.index);
app.get('/login', routes.login);
app.post('/login', routes.doLogin);
app.get('/logout', routes.logout);
app.get('/home', routes.home);
```

注：get为get请求，post为post请求，all为所有针对这个路径的请求

我们打开routes/index.js文件，增加对应的方法。

```{bash}

exports.index = function(req, res){
res.render('index', { title: 'Index' });
};
exports.login = function(req, res){
res.render('login', { title: '用户登陆'});
};
exports.doLogin = function(req, res){
var user={
username:'admin',
password:'admin'
}
if(req.body.username===user.username && req.body.password===user.password){
res.redirect('/home');
}
res.redirect('/login');
};
exports.logout = function(req, res){
res.redirect('/');
};
exports.home = function(req, res){
var user={
username:'admin',
password:'admin'
}
res.render('home', { title: 'Home',user: user});
};
```

创建views/login.html和views/home.html两个文件

### login.html

```{bash}

<% include header.html %>
<div class="container-fluid">
<form class="form-horizontal" method="post">
<fieldset>
<legend>用户登陆</legend>
<div class="control-group">
<label class="control-label" for="username">用户名</label>
<div class="controls">
<input type="text" class="input-xlarge" id="username" name="username">
</div>
</div>
<div class="control-group">
<label class="control-label" for="password">密码</label>
<div class="controls">
<input type="password" class="input-xlarge" id="password" name="password">
</div>
</div>
<div class="form-actions">
<button type="submit" class="btn btn-primary">登陆</button>
</div>
</fieldset>
</form>
</div>
<% include footer.html %>
```

![](http://blog.fens.me/wp-content/uploads/2013/05/login.png)

**注：使用了bootstrap界面框架，效果还不错吧.**

### home.html

```{bash}

<% include header.html %>
<h1>Welcome <%= user.username %>, 欢迎登陆！！</h1>
<a claa="btn" href="/logout">退出</a>
<% include footer.html %>
```

修改index.html，增加登陆链接

### index.html

```{bash}
<% include header.html %>
<h1>Welcome to <%= title %></h1>
<p><a href="/login">登陆</a></p>
<% include footer.html %>
```

路由及页面我们都写好了，快去网站上试试吧。

## 7. Session使用

从刚来的例子上面看，执行exports.doLogin时，如果用户名和密码正确，我们使用redirect方法跳转到的home

```{bash}
res.redirect('/home');
```


执行exports.home时，我们又用render渲染页面，并把user对象传给home.html页面

```{bash}
res.render('home', { title: 'Home',user: user});
```

为什么不能在doLogin时，就把user对象赋值给session，每个页面就不再传值了。

session这个问题，其实是涉及到服务器的底层处理方式。

像Java的web服务器，是多线程调用模型。每用户请求会打开一个线程，每个线程在内容中维护着用户的状态。

像PHP的web服务器，是交行CGI的程序处理，CGI是无状态的，所以一般用cookie在客户的浏览器是维护用户的状态。但cookie在客户端维护的信息是不够的，所以CGI应用要模仿用户session，就需要在服务器端生成一个session文件存储起来，让原本无状态的CGI应用，通过中间文件的方式，达到session的效果。

Nodejs的web服务器，也是CGI的程序无状态的，与PHP不同的地方在于，单线程应用，所有请求都是异步响应，通过callback方式返回数据。如果我们想保存session数据，也是需要找到一个存储，通过文件存储,redis,Mongdb都可以。

接下来，我将演示如何通过mongodb来保存session，并实现登陆后用户对象传递。

app.js文件

```{bash}

var express = require('express')
, routes = require('./routes')
, user = require('./routes/user')
, http = require('http')
, path = require('path')
, ejs = require('ejs')
, SessionStore = require("session-mongoose")(express);
var store = new SessionStore({
url: "mongodb://localhost/session",
interval: 120000
});
....
app.use(express.favicon());
app.use(express.logger('dev'));
app.use(express.bodyParser());
app.use(express.methodOverride());
app.use(express.cookieParser());
app.use(express.cookieSession({secret : 'fens.me'}));
app.use(express.session({
secret : 'fens.me',
store: store,
cookie: { maxAge: 900000 }
}));
app.use(function(req, res, next){
res.locals.user = req.session.user;
next();
});
app.use(app.router);
app.use(express.static(path.join(__dirname, 'public')));
```
**注：app.js文件有顺序要求，一定要注意！！！**

安装session-mongoose依赖库

```{bash}
D:\workspace\project\nodejs-demo>npm install session-mongoose
D:\workspace\project\nodejs-demo\node_modules\session-mongoose\node_modules\mongoose\node_modules\mongodb\node_modules\bson>node "D:\toolkit\nodejs\node_modules\npm\bin\node-gyp-bin\\..\..\node_modules\node-gyp\bin\node-gyp.js" rebuild
C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\Microsoft.Cpp.InvalidPlatform.Targets(23,7): error MSB8007: 项目“kerberos.vcxproj”的平台无效。平台为“x64”。您会看到此消息的可能原因是，您尝试在没有解决方案文件的情况下生成项目，并且为
oose\node_modules\mongoose\node_modules\mongodb\node_modules\bson\build\bson.vcxproj]
session-mongoose@0.2.2 node_modules\session-mongoose
└── mongoose@3.6.10 (mpath@0.1.1, ms@0.1.0, hooks@0.2.1, sliced@0.0.3, muri@0.3.1, mpromise@0.2.1, mongodb@1.3.3)
```

安装有错误但是没关系。

访问：http://localhost:3000/login，正常

修改routes/index.js文件

### exports.doLogin方法

```{bash}

exports.doLogin = function(req, res){
var user={
username:'admin',
password:'admin'
}
if(req.body.username===user.username && req.body.password===user.password){
req.session.user=user;
return res.redirect('/home');
} else {
return res.redirect('/login');
}
};
```

### exports.logout方法

```{bash}
exports.logout = function(req, res){
req.session.user=null;
res.redirect('/');
};
```

### exports.home方法

```{bash}
exports.home = function(req, res){
res.render('home', { title: 'Home'});
};
```

这个时候session已经起作用了，exports.home的user显示传值已经被去掉了。 是通过app.js中app.use的res.locals变量，通过框架进行的赋值。

```{bash}
app.use(function(req, res, next){
res.locals.user = req.session.user;
next();
});
```

注：这个session是express3.0的写法，与express2.x是不一样的。原理是在框架内每次赋值，把我们刚才手动传值的过程，让框架去完成了。

## 8. 页面提示

登陆的大体我们都已经讲完了，最后看一下登陆失败的情况。

我们希望如果用户登陆时，用户名或者密码出错了，会给用户提示，应该如何去实现。

### 打开app.js的，增加res.locals.message

```{bash}
app.use(function(req, res, next){
res.locals.user = req.session.user;
var err = req.session.error;
delete req.session.error;
res.locals.message = '';
if (err) res.locals.message = '<div class="alert alert-error">' + err + '</div>';
next();
});
```

### 修改login.html页面，<%- message %>

```{bash}

<% include header.html %>
<div class="container-fluid">
<form class="form-horizontal" method="post">
<fieldset>
<legend>用户登陆</legend>
<%- message %>
<div class="control-group">
<label class="control-label" for="username">用户名</label>
<div class="controls">
<input type="text" class="input-xlarge" id="username" name="username" value="admin">
</div>
</div>
<div class="control-group">
<label class="control-label" for="password">密码</label>
<div class="controls">
<input type="password" class="input-xlarge" id="password" name="password" value="admin">
</div>
</div>
<div class="form-actions">
<button type="submit" class="btn btn-primary">登陆</button>
</div>
</fieldset>
</form>
</div>
<% include footer.html %>
```

### 修改routes/index.js，增加req.session.error

```{bash}
exports.doLogin = function(req, res){
var user={
username:'admin',
password:'admin'
}
if(req.body.username===user.username && req.body.password===user.password){
req.session.user=user;
return res.redirect('/home');
} else {
req.session.error='用户名或密码不正确';
return res.redirect('/login');
}
};
```

让我们来看看效果： http://localhost:3000/login 输入错误的和密码， 用户名：adminfe，密码：12121

![](http://blog.fens.me/wp-content/uploads/2013/05/loginErr.png)

## 9. 页面访问控制

网站登陆部分按照我们的求已经完成了，但网站并不安全。

localhost:3000/home，页面本来是登陆以后才访问的，现在我们不要登陆，直接在浏览器输入也可访问。

### 页面报错了，提示<%= user.username %> 变量出错。

```{bash}

GET /home?user==a 500 15ms
TypeError: D:\workspace\project\nodejs-demo\views\home.html:2
1| <% include header.html %>
>> 2| <h1>Welcome <%= user.username %>, 欢迎登陆！！</h1>
3| <a claa="btn" href="/logout">退出</a>
4| <% include header.html %>
Cannot read property 'username' of null
at eval (eval at <anonymous> (D:\workspace\project\nodejs-demo\node_modules\ejs\lib\ejs.js:
at eval (eval at <anonymous> (D:\workspace\project\nodejs-demo\node_modules\ejs\lib\ejs.js:
at D:\workspace\project\nodejs-demo\node_modules\ejs\lib\ejs.js:249:15
at Object.exports.render (D:\workspace\project\nodejs-demo\node_modules\ejs\lib\ejs.js:287:
at View.exports.renderFile [as engine] (D:\workspace\project\nodejs-demo\node_modules\ejs\l
at View.render (D:\workspace\project\nodejs-demo\node_modules\express\lib\view.js:75:8)
at Function.app.render (D:\workspace\project\nodejs-demo\node_modules\express\lib\applicati
at ServerResponse.res.render (D:\workspace\project\nodejs-demo\node_modules\express\lib\res
at exports.home (D:\workspace\project\nodejs-demo\routes\index.js:36:8)
at callbacks (D:\workspace\project\nodejs-demo\node_modules\express\lib\router\index.js:161
```

这个页面被打开发，因为没有user.username参数。我们避免这样的错误发生。

还记录路由部分里说的get,post,all的作用吗？我现在要回到路由配置中，再做点事情。

### 修改app.js文件

```{bash}

app.all('/login', notAuthentication);
app.get('/login', routes.login);
app.post('/login', routes.doLogin);
app.get('/logout', authentication);
app.get('/logout', routes.logout);
app.get('/home', authentication);
app.get('/home', routes.home);
```

### 访问控制：

+ / ，谁访问都行，没有任何控制
+ /login，用all拦截所有访问/login的请求，先调用authentication，用户登陆检查
+ /logout，用get拦截访问/login的请求，先调用notAuthentication，用户不登陆检查
+ /home，用get拦截访问/home的请求，先调用Authentication，用户登陆检查

### 修改app.js文件，增加authentication，notAuthentication两个方法

```{bash}

function authentication(req, res, next) {
if (!req.session.user) {
req.session.error='请先登陆';
return res.redirect('/login');
}
next();
}
function notAuthentication(req, res, next) {
if (req.session.user) {
req.session.error='已登陆';
return res.redirect('/');
}
next();
}
```

配置好后，我们未登陆，直接访问localhost:3000/home时，就会跳到/login页面

![](http://blog.fens.me/wp-content/uploads/2013/05/loginHome.png)

如果你也出现图片显示的内容，那么恭喜你了。

Nodejs使用Express3.0框架的第一步你已经完成了，并且还使用了ejs,bootstrap,mongoose库的使用。

希望此文对大家有所帮助。

#### 转载请注明出处：http://blog.fens.me/nodejs-express3/

#### 程序代码已经上传到github有需要的同学，自行下载。
https://github.com/bsspirit/nodejs-demo












