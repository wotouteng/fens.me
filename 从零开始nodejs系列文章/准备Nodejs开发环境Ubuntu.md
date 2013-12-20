准备Nodejs开发环境Ubuntu
================

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-enviroment/

![准备Nodejs开发环境Ubuntu](http://blog.fens.me/wp-content/uploads/2013/06/nodejs-env.jpg)

#### 目录：

1. 通过apt-get安装nodejs –  失败
2. 从github下载源代码安装 –  成功
3. 建立express工程，启动第一个项目

#### 系统环境：
Ubuntu 12.04 LTS 64bit

## 1. 通过apt-get安装nodejs – 失败：

```{bash}

~ sudo apt-get install nodejs
~ sudo apt-get install npm

~ node -v
v0.6.12

~ npm -v
1.1.4
```

### 创建工作目录

```{bash}
~ mkdir workspace
~ mkdir workspace/nodejs
~ cd workspace/nodejs
~ pwd
/home/conan/workspace/nodejs
```

### 安装失败

```{bash}
sudo npm install express -g
npm http GET https://registry.npmjs.org/express
npm http 304 https://registry.npmjs.org/express
npm http GET https://registry.npmjs.org/connect/2.7.11
npm http GET https://registry.npmjs.org/commander/0.6.1
npm http GET https://registry.npmjs.org/range-parser/0.0.4
npm http GET https://registry.npmjs.org/mkdirp/0.3.4
npm http GET https://registry.npmjs.org/cookie/0.1.0
npm http GET https://registry.npmjs.org/buffer-crc32/0.2.1
npm http GET https://registry.npmjs.org/fresh/0.1.0
npm http GET https://registry.npmjs.org/methods/0.0.1
npm http GET https://registry.npmjs.org/send/0.1.0
npm http GET https://registry.npmjs.org/cookie-signature/1.0.1
npm http GET https://registry.npmjs.org/debug
npm http 304 https://registry.npmjs.org/commander/0.6.1
npm http 304 https://registry.npmjs.org/connect/2.7.11
npm http 304 https://registry.npmjs.org/range-parser/0.0.4
npm http 304 https://registry.npmjs.org/mkdirp/0.3.4
npm http 304 https://registry.npmjs.org/cookie/0.1.0
npm http 304 https://registry.npmjs.org/buffer-crc32/0.2.1
npm http 304 https://registry.npmjs.org/fresh/0.1.0
npm http 304 https://registry.npmjs.org/methods/0.0.1
npm http 304 https://registry.npmjs.org/send/0.1.0
npm http 304 https://registry.npmjs.org/cookie-signature/1.0.1
npm http 304 https://registry.npmjs.org/debug
npm ERR! error installing express@3.2.6
npm ERR! error rolling back express@3.2.6 Error: UNKNOWN, unknown error '/usr/local/lib/node_modules/express'

npm ERR! Unsupported
npm ERR! Not compatible with your version of node/npm: connect@2.7.11
npm ERR! Required: {"node":">= 0.8.0"}
npm ERR! Actual: {"npm":"1.1.4","node":"0.6.12"}
npm ERR!
npm ERR! System Linux 3.5.0-23-generic
npm ERR! command "node" "/usr/bin/npm" "install" "express" "-g"
npm ERR! cwd /home/conan/workspace/nodejs
npm ERR! node -v v0.6.12
npm ERR! npm -v 1.1.4
npm ERR! code ENOTSUP
npm ERR! message Unsupported
npm ERR! errno {}
npm http GET https://registry.npmjs.org/mime/1.2.6
npm ERR!
npm ERR! Additional logging details can be found in:
npm ERR! /home/conan/workspace/nodejs/npm-debug.log
npm not ok
```

系统提示, node和npm版本不兼容。可能是终于apt-get源没有更新造成的问题。

## 2. 从github下载源代码安装 – 成功

下面要找到兼容的版本，手动安装。
  先卸载刚刚装的node和npm

```{bash}
~ sudo apt-get autoremove npm
~ sudo apt-get autoremove nodejs
```

找到nodejs的官方发布下载：https://github.com/joyent/node

### 在ubuntu中，先安装git

```{bash}

~ sudo apt-get install git
```

### 然后，从github下载nodejs源代码

```{bash}
~ git clone git://github.com/joyent/node.git
Cloning into 'node'...
remote: Counting objects: 100200, done.
remote: Compressing objects: 100% (28074/28074), done.
remote: Total 100200 (delta 78807), reused 90936 (delta 70473)
Receiving objects: 100% (100200/100200), 61.81 MiB | 698 KiB/s, done.
Resolving deltas: 100% (78807/78807), done.
```

进入node目录

```{bash}
~ cd node
~ pwd
/home/conan/workspace/nodejs/node
```

切换最新的release的版本v0.11.2-release

```{bash}
~ git checkout v0.11.2-release
Branch v0.11.2-release set up to track remote branch v0.11.2-release from origin.
Switched to a new branch 'v0.11.2-release'
```

### 进行安装

```{bash}
./configure
make
sudo make install
```

### 安装完成，查看node版本

```{bash}
~ node -v
-bash: /usr/bin/node: No such file or directory
```

提示错误，没有找到node，查一下node安装位置

```{bash}
~ whereis node
node: /usr/local/bin/node
```

### 增加软链接：node和npm到/usr/bin

```{bash}
~ sudo ln -s /usr/local/bin/node /usr/bin/node
~ sudo ln -s /usr/local/bin/npm /usr/bin/npm
```

### 我们再查看node和npm版本

```{bash}
~ node -v
v0.11.2
~ npm -v
1.2.21
```

### 下面安装express

```{bash}
~ sudo npm install express -g
express@3.2.6 /usr/local/lib/node_modules/express
├── methods@0.0.1
├── fresh@0.1.0
├── range-parser@0.0.4
├── cookie-signature@1.0.1
├── buffer-crc32@0.2.1
├── cookie@0.1.0
├── debug@0.7.2
├── commander@0.6.1
├── mkdirp@0.3.4
├── send@0.1.0 (mime@1.2.6)
└── connect@2.7.11 (pause@0.0.1, qs@0.6.5, bytes@0.2.0, cookie@0.0.5, formidable@1.0.14, send@0.1.1)
```

安装成功。

## 3. 建立express工程，启动第一个项目

```{bash}
~ express -e nodejs-demo
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

### 安装依赖包

```{bash}
~ cd nodejs-demo
~ sudo npm install
express@3.2.6 node_modules/express
├── methods@0.0.1
├── fresh@0.1.0
├── range-parser@0.0.4
├── cookie-signature@1.0.1
├── buffer-crc32@0.2.1
├── cookie@0.1.0
├── debug@0.7.2
├── commander@0.6.1
├── mkdirp@0.3.4
├── send@0.1.0 (mime@1.2.6)
└── connect@2.7.11 (pause@0.0.1, qs@0.6.5, bytes@0.2.0, cookie@0.0.5, formidable@1.0.14, send@0.1.1)
```

### 启动程序

```{bash}
~ node app.js
Express server listening on port 3000
```


### 测试是否启动成功curl

```{bash}
~ sudo apt-get install curl
~ curl localhost:3000
```

```{bash}
<!DOCTYPE html>
<html>
<head>
<title>Express</title>
<link rel='stylesheet' href='/stylesheets/style.css' />
</head>
<body>
<h1>Express</h1>
<p>Welcome to Express</p>
</body>
</html>
```

### nodejs的服务器日志：

```{bash}
GET / 200 6ms - 206b
```

好了，我们已经成功的在ubuntu中，准备好了nodejs的开发环境。下面就可以享受开发的乐趣了。

**进阶的内容请继续看**
+ [从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)
  http://blog.fens.me/series-nodejs/

#### 转载请注明出处：http://blog.fens.me/nodejs-enviroment/













