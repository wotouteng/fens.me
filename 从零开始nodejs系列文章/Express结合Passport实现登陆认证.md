Express结合Passport实现登陆认证
=========

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-express-passport/

![Express结合Passport实现登陆认证](http://blog.fens.me/wp-content/uploads/2014/02/nodejs-passport.png)

#### 前言

登陆认证，是每个应用都需要的基础功能。但很多的时候，却都被大家所忽略，不仅安全漏洞严重，而且代码紧耦合，混乱不堪。

Passport项目，正是为了解决登陆认证的事情，让认证模块更透明，减少耦合！

#### 目录

1. 什么是认证(Authentication)？
2. Passport项目介绍
3. Express结合Passport实现登陆认证

## 1. 什么是登陆认证(Authentication)？

认证又称“验证”、“鉴权”，是指通过一定的手段，完成对用户身份的确认。身份验证的方法有很多，基本上可分为：基于共享密钥的身份验证、基于生物学特征的身份验证和基于公开密钥加密算法的身份验证。

登陆认证，是用户在访问应用或者网站时，通过是先注册的用户名和密码，告诉应用使用者的身份，从而获得访问权限的一种操作。

几乎所有的应用都需要登陆认证！

## 2. Passport项目介绍

Passport项目是一个基于Nodejs的认证中间件。Passport目的只是为了“登陆认证”，因此，代码干净，易维护，可以方便地集成到其他的应用中。

Web应用一般有2种登陆认证的形式：

* 用户名和密码认证登陆
* OAuth认证登陆

Passport可以根据应用程序的特点，配置不同的认证机制。本文将介绍，用户名和密码的认证登陆。

项目网站：http://passportjs.org/

## 3. Express结合Passport实现登陆认证

### 系统环境：

* Win7 64bit 旗舰版
* node v0.10.5
* npm 1.2.19

### 1). 新建项目

```{bash}

D:\workspace\javascript>express -e nodejs-passport
D:\workspace\javascript>cd nodejs-passport && npm install
D:\workspace\javascript\nodejs-passport>npm install passport
D:\workspace\javascript\nodejs-passport>npm install passport-local
```

### 2). 实现Session的认证：

* 启用connet的session中间件
* connet的session中间件，同时依赖于connect的cookieParser中间件
* 配置passport中间件

关于connect框架的详细说明，请参考文章：[Nodejs基础中间件Connect](http://blog.fens.me/nodejs-connect/)

修改app.js

```{bash}

app.use(express.cookieParser())
app.use(express.session({secret: 'blog.fens.me', cookie: { maxAge: 60000 }}));
app.use(passport.initialize());
app.use(passport.session());
```

### 3). 定义认证策略：

LocalStrategy策略，用于匹配本地环境的用户名和密码，可以扩展这个策略，通过数据库的方式进行匹配。

修改app.js

```{bash}

var passport = require('passport')
    , LocalStrategy = require('passport-local').Strategy;

passport.use('local', new LocalStrategy(
    function (username, password, done) {
        var user = {
            id: '1',
            username: 'admin',
            password: 'pass'
        }; // 可以配置通过数据库方式读取登陆账号

        if (username !== user.username) {
            return done(null, false, { message: 'Incorrect username.' });
        }
        if (password !== user.password) {
            return done(null, false, { message: 'Incorrect password.' });
        }

        return done(null, user);
    }
));

passport.serializeUser(function (user, done) {//保存user对象
    done(null, user);//可以通过数据库方式操作
});

passport.deserializeUser(function (user, done) {//删除user对象
    done(null, user);//可以通过数据库方式操作
});
```

### 4). 路由控制和登陆认证

路由页面

* /: 首页，用于登陆，未登陆用户只能访问首页
* /login: 登陆请求，用户登陆时，POST到登陆请求，认证成功跳到用户页，认证失败回到首页
* /users: 用户页，用户通过登陆认证后，可以访问用户页
* /logout: 登出请求，用户退出系统，GET到登出请求，页面自动跳回首页

修改app.js

```{bash}

app.get('/', routes.index);
app.post('/login',
    passport.authenticate('local', {
        successRedirect: '/users',
        failureRedirect: '/'
    }));

app.all('/users', isLoggedIn);
app.get('/users', user.list);
app.get('/logout', function (req, res) {
    req.logout();
    res.redirect('/');
});

function isLoggedIn(req, res, next) {
    if (req.isAuthenticated())
        return next();

    res.redirect('/');
}
```

### 5). 运行程序

![](http://blog.fens.me/wp-content/uploads/2014/02/passport-login.png)

运行日志

```{bash}
D:\workspace\javascript\nodejs-passport>node app.js
Express server listening on port 3000
POST /login 302 389ms - 68b
GET /users 200 2ms - 50b
GET /logout 302 2ms - 58b
GET / 200 7ms - 540b
GET /stylesheets/style.css 304 6ms
POST /login 302 2ms - 58b
GET / 200 2ms - 540b
GET /stylesheets/style.css 304 2ms
```

### 6). 完整的代码

* app.js代码
* index.ejs代码
* user.js代码

app.js代码

```{bash}

var express = require('express')
    , routes = require('./routes')
    , user = require('./routes/user')
    , http = require('http')
    , path = require('path')
    , app = express();

var passport = require('passport')
    , LocalStrategy = require('passport-local').Strategy;

app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.set('view engine', 'ejs');
app.use(express.favicon());
app.use(express.logger('dev'));
app.use(express.bodyParser());
app.use(express.methodOverride());
app.use(express.cookieParser())
app.use(express.session({secret: 'blog.fens.me', cookie: { maxAge: 60000 }}));
app.use(passport.initialize());
app.use(passport.session());
app.use(app.router);
app.use(express.static(path.join(__dirname, 'public')));

if ('development' == app.get('env')) {
    app.use(express.errorHandler());
}

passport.use('local', new LocalStrategy(
    function (username, password, done) {
        var user = {
            id: '1',
            username: 'admin',
            password: 'pass'
        }; // 可以配置通过数据库方式读取登陆账号

        if (username !== user.username) {
            return done(null, false, { message: 'Incorrect username.' });
        }
        if (password !== user.password) {
            return done(null, false, { message: 'Incorrect password.' });
        }

        return done(null, user);
    }
));

passport.serializeUser(function (user, done) {//保存user对象
    done(null, user);//可以通过数据库方式操作
});

passport.deserializeUser(function (user, done) {//删除user对象
    done(null, user);//可以通过数据库方式操作
});

app.get('/', routes.index);
app.post('/login',
    passport.authenticate('local', {
        successRedirect: '/users',
        failureRedirect: '/'
    }));

app.all('/users', isLoggedIn);
app.get('/users', user.list);
app.get('/logout', function (req, res) {
    req.logout();
    res.redirect('/');
});

http.createServer(app).listen(app.get('port'), function () {
    console.log('Express server listening on port ' + app.get('port'));
});

function isLoggedIn(req, res, next) {
    if (req.isAuthenticated())
        return next();

    res.redirect('/');
}
```

index.ejs代码

```{bash}

<!DOCTYPE html>
<html>
<head>
<title><%= title %></title>
<link rel='stylesheet' href='/stylesheets/style.css' />
</head>
<body>
<h1>Login</h1>
<form action="/login" method="post">
<div>
<label>Username:</label>
<input type="text" name="username"/>
</div>
<div>
<label>Password:</label>
<input type="password" name="password"/>
</div>
<div>
<input type="submit" value="Log In"/>
</div>
</form>
</body>
</html>
```

user.js代码

```{bash}
exports.list = function (req, res) {
var html = "<h2>你好, " + req.user.username + "</h2><a href='/logout'>退出</a>";
res.send(html);
};
```

通过Passport中间件，我们就把登陆认证和应用程序分离了出来，从而保证了更清晰代码结构。当然，我们也可不用Passport，在Express中直接实现登陆认证，可以参考文章：[Nodejs开发框架Express3.0开发手记–从零开始](http://blog.fens.me/nodejs-express3/)

#### 转载请注明出处：http://blog.fens.me/nodejs-express-passport/

