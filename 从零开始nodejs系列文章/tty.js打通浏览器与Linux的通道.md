tty.js打通浏览器与Linux的通道
==========

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-linux-sh-tty/

![tty.js打通浏览器与Linux的通道](http://blog.fens.me/wp-content/uploads/2013/11/nodejs-linux-tty.png)

#### 前言

长久以来，远程连接Linux服务器时，已经习惯了用SSH客户端(Putty,SshClient)，黑底白字的显示方式，在记忆里留下了深深地烙印。如果有一种新的连接方式，不需要安装额外客户端软件，通过web浏览器就可以实现，那么一定会让你惊喜吧！

tty.js就实现这个功能，让编程更多乐趣，更少磨难。

#### 目录

1. Linux Shell介绍
2. tty.js介绍
3. tty.js安装
4. tty.js使用

## 1. Linux Shell介绍

Linux Shell（Command Line Interface shell ，即CLI shell）是系统的用户界面，提供了用户与内核进行交互操作的一种接口。它接收用户输入的命令并把它送入内核去执行。

实际上Shell是一个命令解释器，它解释由用户输入的命令并且把它们送到内核。不仅如此，Shell有自己的编程语言用于对命令的编辑，它允许用户编写由shell命令组成的程序。Shell编程语言具有普通编程语言的很多特点，比如它也有循环结构和分支控制结构等，用这种编程语言编写的Shell程序与其他应用程序具有同样的效果。

上文介绍，摘自：http://baike.baidu.com/view/573462.htm

一般Linux作为服务器都放在机房，通过ssh远程登录Linux主机。Putty是SSH的一种客户端实现，下面我们用putty进行远程登陆。

![](http://blog.fens.me/wp-content/uploads/2013/11/tty-putty-login.png)

登陆的的命令界面

![](http://blog.fens.me/wp-content/uploads/2013/11/tty-putty.png)

## 2. tty.js介绍

tty.js是一个支持在浏览器中运行的命令行窗口，基于node.js平台，依赖socket.io库，通过websocket与Linux系统通信。

### 特性：

* 支持多tab窗口模型
* 支持vim,mc,irssi,vifm语法
* 支持xterm鼠标事件
* 支持265色显示
* 支持session

github源代码：https://github.com/chjj/tty.js

## 3. tty.js安装

### 系统环境：

* Linux: Ubuntu 12.04 LTS 64bit
* node: v0.6.12
* npm: 1.1.4

安装tty.js

```{bash}

~ mkdir /home/conan/nodejs/nodejs-tty
~ cd /home/conan/nodejs/nodejs-tty

~ sudo npm install tty.js
tty.js@0.2.13 node_modules/tty.js
├── term.js@0.0.3
├── pty.js@0.2.3
├── express@3.1.0 (methods@0.0.1, fresh@0.1.0, range-parser@0.0.4, cookie-signature@0.0.1, debug@0.7.3, buffer-crc32@0.1.1, cookie@0.0.5, commander@0.6.1, mkdirp@0.3.3, send@0.1.0, connect@2.7.2)
└── socket.io@0.9.13 (base64id@0.1.0, policyfile@0.0.4, redis@0.7.3,socket.io-client@0.9.1
```

配置tty.js，新建两个文件

* tty.js:node的启动文件
* .tty.js:参数的配置文件

### 1). tty.js

设置参数：

* 登陆账号：foo
* 登陆密码：bar
* 脚本方言：bash
* web访问端口：8000

```{bash}
~ vi tty.js

var tty = require('tty.js');

var app = tty.createServer({
  shell: 'bash',
  users: {
    foo: 'bar'
  },
  port: 8000
});

app.get('/foo', function(req, res, next) {
  res.send('bar');
});

app.listen();
```

### 2). .tty.js

```{bash}
~ vi .tty.js

{
  "users": {
    "hello": "world"
  },
  "https": {
    "key": "./server.key",
    "cert": "./server.crt"
  },
  "port": 8080,
  "hostname": "127.0.0.1",
  "shell": "sh",
  "shellArgs": ["arg1", "arg2"],
  "static": "./static",
  "limitGlobal": 10000,
  "limitPerUser": 1000,
  "localOnly": false,
  "cwd": ".",
  "syncSession": false,
  "sessionTimeout": 600000,
  "log": true,
  "io": { "log": false },
  "debug": false,
  "term": {
    "termName": "xterm",
    "geometry": [80, 24],
    "scrollback": 1000,
    "visualBell": false,
    "popOnBell": false,
    "cursorBlink": false,
    "screenKeys": false,
    "colors": [
      "#2e3436",
      "#cc0000",
      "#4e9a06",
      "#c4a000",
      "#3465a4",
      "#75507b",
      "#06989a",
      "#d3d7cf",
      "#555753",
      "#ef2929",
      "#8ae234",
      "#fce94f",
      "#729fcf",
      "#ad7fa8",
      "#34e2e2",
      "#eeeeec"
    ]
  }
}
```

## 4. tty.js使用

启动tty.js服务器

```{bash}
~ node tty.js
[tty.js] You should sha1 your user information.
[tty.js] Listening on port 8000.
```

打开web界面：http://192.168.1.20:8000

登陆后，我们就可以在web上使用Linux命令了，与putty是一样的

![](http://blog.fens.me/wp-content/uploads/2013/11/tty-console.png)

使用起来比Putty爽多了！！

非常简单地配置，就搭建了一个WebShell服务器端程序。技术正在一点点的改变着世界，你是否发现了？！

#### 转载请注明出处：http://blog.fens.me/nodejs-linux-sh-tty/

