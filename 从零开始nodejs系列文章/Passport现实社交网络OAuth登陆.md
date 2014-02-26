Passport现实社交网络OAuth登陆
============

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-oauth-passport/

![](http://blog.fens.me/wp-content/uploads/2014/02/nodejs-passport-oauth.png)

#### 前言

随着社交网络的发展，开发一个应用门槛越来越低。从一个完整的应用系统，到一个部署在社交网络平台的APP；从数据库–》应用层–》展示层，变成只需要开发展示层。

很多的社交应用，甚至都放弃了用户注册！仅靠大型社交网站的登陆授权，就可以赚到100W以上的用户量。。。

减少用户管理代码开发及维护，更专注于应用本身，个人开发者已经崛起！！

#### 目录

1. Passport介绍
2. OAuth介绍
3. 登陆Github
4. 登陆LinkedIn

## 1. Passport介绍

Passport项目，主要是为了解决登陆认证的问题。

Web应用一般有2种登陆认证的形式：

* 用户名和密码认证登陆
* OAuth认证登陆

在上一篇文章中，我们介绍了Passport的项目，通过用户名和密码认证登陆。[Express结合Passport实现登陆认证](http://blog.fens.me/nodejs-express-passport/)

本文将介绍，通过Passport实现OAuth登陆认证。

## 2. OAuth介绍

OAuth协议为用户资源的授权提供了一个安全的、开放而又简易的标准。与以往的授权方式不同之处是OAuth的授权不会使第三方触及到用户的帐号信息（如用户名与密码），即第三方无需使用用户的用户名与密码就可以申请获得该用户资源的授权，因此OAuth是安全的。

* 简单：不管是OAUTH服务提供者还是应用开发者，都很易于理解与使用
* 安全：没有涉及到用户密钥等信息，更安全更灵活
* 开放：任何服务提供商都可以实现OAUTH，任何软件开发商都可以使用OAUTH

OAuth认证授权就三个步骤，三句话可以概括：

* 获取未授权的Request Token
* 获取用户授权的Request Token
* 用授权的Request Token换取Access Token

> OAuth的介绍，摘自：http://baike.baidu.com/view/3948029.htm

## 3. Github登陆

通过Passport实现Github登陆。我们还使用上一篇文章中的环境：[Express结合Passport实现登陆认证](http://blog.fens.me/nodejs-express-passport/)

* 申请开发github应用
* Passport程序实现
* 运行Github登陆认证

### 1). 申请开发github应用

![](http://blog.fens.me/wp-content/uploads/2014/02/github-app.png)

### 2). Passport程序实现

* 安装Passport的github扩展
* 增加Github认证策略
* 定义Github认证的路由配置: 登陆，回调，展示

a. 安装Passport的github扩展

```{bash}

D:\workspace\javascript\nodejs-passport>npm install passport-github
```

b. 增加Github认证策略

修改app.js

```{bash}
passport.use(new GithubStrategy({//对应从Github申请KEY
    clientID: "XXXX",
    clientSecret: "XXXX",
    callbackURL: "http://localhost:3000/auth/github/callback"
},function(accessToken, refreshToken, profile, done) {
    done(null, profile);
}));
```

c. 定义Github认证的路由配置: 登陆，回调，展示

* /auth/github: 通过github，登陆
* /auth/github/callback: github认证成功后，回调
* /github: 回调验证后，转向展示示

修改app.js

```{bash}
app.all('/github', isLoggedIn);
app.get("/github",user.github);

app.get("/auth/github", passport.authenticate("github",{ scope : "email"}));
app.get("/auth/github/callback",
    passport.authenticate("github",{
        successRedirect: '/github',
        failureRedirect: '/'
    }));
```

### 3).运行Github登陆认证

![](http://blog.fens.me/wp-content/uploads/2014/02/github-auth.png)

程序日志

```{bash}
D:\workspace\javascript\nodejs-passport>node app.js
Express server listening on port 3000
GET / 200 401ms - 594b
GET /stylesheets/style.css 304 9ms
GET /auth/github 302 8ms - 424b
GET /auth/github/callback?code=7cf818c4590e2aacfe90 302 4052ms - 70b
GET /github 200 4ms - 139b
GET /logout 302 2ms - 58b
GET / 200 3ms - 594b
GET /stylesheets/style.css 304 2ms
```

## 4. LinkedIn登陆

* 申请开发LinkedIn应用
* Passport程序实现
* 运行LinkedIn登陆认证

### 1). 申请开发LinkedIn应用

![](http://blog.fens.me/wp-content/uploads/2014/02/linkedin-app.png)

### 2). Passport程序实现

* 安装Passport的LinkedIn扩展
* 增加LinkedIn认证策略
* 定义LinkedIn认证的路由配置: 登陆，回调，展示

a. 安装Passport的LinkedIn扩展

```{bash}
D:\workspace\javascript\nodejs-passport>npm install passport-linkedin
```

b. 增加LinkedIn认证策略

修改app.js

```{bash}
passport.use(new LinkedinStrategy({
    consumerKey: "XXXX",
    consumerSecret: "XXXX",
    callbackURL: "http://localhost:3000/auth/linkedin/callback",
    userAgent: 'localhost'
},function(accessToken, refreshToken, profile, done) {
    done(null, profile);
}));
```

c. 定义LinkedIn认证的路由配置: 登陆，回调，展示

* /auth/linkedin: 通过LinkedIn，登陆
* /auth/linkedin/callback: github认证成功后，回调
* /linkedin: 回调验证后，转向展示示

修改app.js

```{bash}
app.all('/github', isLoggedIn);
app.get("/github",user.github);

app.all('/linkedin', isLoggedIn);
app.get("/linkedin",user.linkedin);
app.get("/auth/linkedin", passport.authenticate("linkedin",{}));
app.get("/auth/linkedin/callback",
    passport.authenticate("linkedin",{
        successRedirect: '/linkedin',
        failureRedirect: '/'
    }));
```

### 3).运行LinkedIn登陆认证

![](http://blog.fens.me/wp-content/uploads/2014/02/linkedin-auth.png)

程序日志

```{bash}
D:\workspace\javascript\nodejs-passport>node app.js
Express server listening on port 3000
GET / 200 399ms - 638b
GET /stylesheets/style.css 304 4ms
GET /auth/Linkedin 302 3092ms - 256b
GET /auth/linkedin/callback?oauth_token=75--8f032180-afae-489b-bc3c-3326d80bea6f&oauth_verifier=36091 302 3049ms - 74b
GET /linkedin 200 5ms - 76b
GET /logout 302 1ms - 58b
GET / 200 19ms - 638b
GET /stylesheets/style.css 304 2ms
```

## 5. 完整的应用

项目代码我已上传到了github, 项目地址：https://github.com/bsspirit/nodejs-passport

下载及安装

```{bash}
git clone https://github.com/bsspirit/nodejs-passport
npm install
```

我们非常方便地，实现了Github和LinkedIn登陆认证，是否已经体会到Passport的强大了！把权限这种基础功能，进行合理的封装，是会帮我们节省大量的工作量的。

#### 转载请注明出处：http://blog.fens.me/nodejs-oauth-passport/


