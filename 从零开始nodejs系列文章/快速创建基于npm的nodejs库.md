快速创建基于npm的nodejs库
============

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-npm-package

![快速创建基于npm的nodejs库](http://blog.fens.me/wp-content/uploads/2013/12/nodejs-npm.png)

#### 前言

用Node实现的功能越来越多，代码越来越复杂，我们就开始考虑如何把代码从项目中抽出来，形成单独的类库(模块)管理。就像我们所依赖其他第三方类库一样。

本文将介绍如何定义开发一个类库，包括命令开发，发布到npm中，让其他人也可以使用。

#### 目录

1. npm是什么？
2. 快速创建包
3. 创建命令行工具
4. 本地安装lowercase包
5. 项目上传到github
6. 通过npm发布包
7. 通过npm安装包

## 1. npm是什么？

NPM的全称是，是一个NodeJS包管理和分发工具，已经成为了非官方的发布Node模块的标准。

Nodejs自身提供了基本的模块，但是开发实际应用过程中仅仅依靠这些基本模块则还需要较多的工作。幸运的是，Nodejs库和框架为我们提供了帮助，让我们减少工作量。但是成百上千的库或者框架管理起来又很麻烦，有了NPM，可以很快的找到特定服务要使用的包，进行下载、安装以及管理已经安装的包。

类似于Java中的Maven，Ubuntu中的apt-get, Ruby中的Gem, Python中pypi等…

NPM模块发布页：https://npmjs.org/

## 2. 快速创建包

项目描述：读取文件，把所有的大写文字转换成小写文字，控制台输出。

### 系统环境：

* win7 64bit
* Nodejs:v0.10.5
* Npm:1.2.19

创建项目

```{bash}
~ D:\workspace\javascript>mkdir nodejs-package && cd nodejs-package
```

创建项目结构

```{bash}

~ D:\workspace\javascript\nodejs-package>mkdir bin
~ D:\workspace\javascript\nodejs-package>touch bin/lowercase
~ D:\workspace\javascript\nodejs-package>touch bin/lowercase.bat
~ D:\workspace\javascript\nodejs-package>mkdir test
~ D:\workspace\javascript\nodejs-package>mkdir test/data
~ D:\workspace\javascript\nodejs-package>touch test/data/sample.txt
~ D:\workspace\javascript\nodejs-package>touch lowercase.js
~ D:\workspace\javascript\nodejs-package>touch example.js
~ D:\workspace\javascript\nodejs-package>touch README.md
```

![](http://blog.fens.me/wp-content/uploads/2013/12/nodejs-package-folder.png)

项目结构说明

* bin目录: 用于存放命令的目录
* bin/lowercase文件: Linux命令行可执行文件
* bin/lowercase.bat文件: Win命令行可执行文件
* test目录: 用于存放测试代码的目录
* test/data目录: 用于存放测试数据的目录
* test/data/sample.txt: 测试数据文件
* lowercase.js文件: 核心功能代码文件
* example.js文件: 例子代码文件
* package.json文件: 项目描述及依赖文件
* README.md文件: 项目说明文件

### 1). 创建文件：package.json

```{bash}

~ D:\workspace\javascript\nodejs-package>npm init
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sane defaults.

See `npm help json` for definitive documentation on these fields
and exactly what they do.

Use `npm install  --save` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
name: (nodejs-package) lowercase
version: (0.0.0) 0.0.1
description: A demo package
entry point: (index.js)
test command:
git repository:
keywords:
author: Conan Zhang
license: (BSD) MIT
About to write to D:\workspace\javascript\nodejs-package\package.json:

{
  "name": "lowercase",
  "version": "0.0.1",
  "description": "A demo package",
  "main": "index.js",
  "directories": {
    "test": "test"
  },
  "dependencies": {
    "moment": "~2.4.0"
  },
  "devDependencies": {},
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": "",
  "author": "Conan Zhang",
  "license": "MIT",
  "readmeFilename": "README.md"
}

Is this ok? (yes) yes
npm WARN package.json lowercase@0.0.1 No readme data!
```

### 修改文件：package.json

```{bash}

~ vi package.json

{
    "name": "lowercase_demo",
    "version": "0.0.1",
    "description": "A demo package of lowercase",
    "keywords":[
        "demo","lowercase"
    ],
    "author": "Conan Zhang  (http://blog.fens.me)",
    "license": "MIT",
    "main": "lowercase.js",
    "repository": {
        "type":"git",
        "url":"https://github.com/bsspirit/lowercase_demo"
    },
    "engines": {
        "node": ">=v0.10.5"
    },
    "readmeFilename": "README.md"
}
```

### 2). 编辑文件：lowercase.js

```{bash}

~ vi lowercase.js

"use strict"
var fs = require('fs');

exports.lowerCase = function (myfile) {
    console.log(myfile);

    if (fs.existsSync(myfile)) {
        var content = fs.readFileSync(myfile, 'utf8');
        console.log(content.toLowerCase());
    } else {
        console.log("File does not exist - " + myfile);
    }
}
```

### 3). 编辑文件：example.js

```{bash}
~ vi example

"use strict"

var lowercase = require('./lowercase.js');
var myfile="test/data/sample.txt"
lowercase.lowerCase(myfile);
```

### 4). 编辑文件：test/data/sample.txt

```{bash}
~ vi sample.txt

JAVA
NODEJS
PYTHON
PHP
.NET
R
RUBY
C
C++
GO
```

### 5). 运行：example.js

```{bash}
~ D:\workspace\javascript\nodejs-package>node example.js
test/data/sample.txt
java
nodejs
python
php
.net
r
ruby
c
c++
go
```

### 6). 编辑文件：README.md

```{bash}
  ~ vi README.md

  lowercase
  ========================

  A demo package of lowercase

  ```{bash}
  npm install lowercase_demo
  ```

  ## author

  Conan Zhang, http://blog.fens.me

  ## License

  MIT
```

## 3. 创建命令行工具

### 1). 编辑文件：bin/lowercase

```{bash}
~ vi lowercase

#!/usr/bin/env node

var myfile = process.argv.slice(2);

var path = require('path');
var fs = require('fs');
var dir = path.dirname(fs.realpathSync(__filename))+"/../";
require(dir+'lowercase.js').lowerCase(dir+myfile);
```

### 2). 编辑文件：bin/lowercase.bat

```{bash}
~ vi lowercase.bat

node.exe bin/lowercase %1
```

### 3). 运行lowercase.bat

```{bash}
~ D:\workspace\javascript\nodejs-package>bin\lowercase.bat test\data\sample.txt

D:\workspace\javascript\nodejs-package>node.exe bin/lowercase test\data\sample.txt
D:\workspace\javascript\nodejs-package\bin/../test\data\sample.txt
java
nodejs
python
php
.net
r
ruby
c
c++
go
```

## 4. 本地安装lowercase包

新建项目，并安装lowercase依赖库

```{bash}
~ D:\workspace\javascript>mkdir nodejs-package-project && cd nodejs-package-project
~ D:\workspace\javascript\nodejs-package-project>npm install ..\nodejs-package
lowercase_demo@0.0.1 node_modules\lowercase_demo
```

![](http://blog.fens.me/wp-content/uploads/2013/12/nodejs-package-project.png)

新建运行文件：app.js

```{bash}
~ vi app.js

var lowercase = require('lowercase_demo');
var myfile="data.txt"
lowercase.lowerCase(myfile);
```

数据文件: data.txt

```{bash}
~ vi data.txt

APP
BACCDADDS
```

运行app.js

```{bash}
~ D:\workspace\javascript\nodejs-package-project>node app.js
data.txt
app
baccdadds
```

## 5. 项目上传到github

把lowercase_demo库，上传到github。

```{bash}
~ git init
~ git add .
~ git commit -m 'init'
~ git remote add origin git@github.com:bsspirit/lowercase_demo.git
~ git push origin master
```

项目github地址：https://github.com/bsspirit/lowercase_demo

注：大家可以基于这个demo项目基础上，继续完成包的开发。

## 6. 通过npm发布包

在npm上，注册新用户

```{bash}
~ D:\workspace\javascript\nodejs-package>npm adduser
Username: bsspirit
Password:
Email: bsspirit@gmail.com
npm http PUT https://registry.npmjs.org/-/user/org.couchdb.user:bsspirit
npm http 409 https://registry.npmjs.org/-/user/org.couchdb.user:bsspirit
npm http GET https://registry.npmjs.org/-/user/org.couchdb.user:bsspirit
npm http 200 https://registry.npmjs.org/-/user/org.couchdb.user:bsspirit
npm http PUT https://registry.npmjs.org/-/user/org.couchdb.user:bsspirit/-rev/2-25eae797548e61
npm http 201 https://registry.npmjs.org/-/user/org.couchdb.user:bsspirit/-rev/2-25eae797548e61
```

在npm上，发布项目

```{bash}

~ D:\workspace\javascript\nodejs-package>npm publish
npm http PUT https://registry.npmjs.org/lowercase_demo
npm http 201 https://registry.npmjs.org/lowercase_demo
npm http GET https://registry.npmjs.org/lowercase_demo
npm http 200 https://registry.npmjs.org/lowercase_demo
npm http PUT https://registry.npmjs.org/lowercase_demo/-/lowercase_demo-0.0.1.tgz/-rev/1-162a1
e
npm http 201 https://registry.npmjs.org/lowercase_demo/-/lowercase_demo-0.0.1.tgz/-rev/1-162a1
e
npm http PUT https://registry.npmjs.org/lowercase_demo/0.0.1/-tag/latest
npm http 201 https://registry.npmjs.org/lowercase_demo/0.0.1/-tag/latest
+ lowercase_demo@0.0.1
```

## 7. 通过npm安装

通过npm下载安装

```{bash}
~ D:\workspace\javascript>mkdir nodejs-package-project2 && cd nodejs-package-project2
~ D:\workspace\javascript\nodejs-package-project2>npm install lowercase_demo
npm http GET https://registry.npmjs.org/lowercase_demo
npm http 200 https://registry.npmjs.org/lowercase_demo
lowercase_demo@0.0.1 node_modules\lowercase_demo
```

全局安装lowercase_demo

```{bash}
~ D:\workspace\javascript\nodejs-package-project2>npm install lowercase_demo -g
npm http GET https://registry.npmjs.org/lowercase_demo
npm http 304 https://registry.npmjs.org/lowercase_demo
D:\toolkit\nodejs\lowercase -> D:\toolkit\nodejs\node_modules\lowercase_demo\bin\lowercase
npm ERR! peerinvalid The package generator-karma does not satisfy its siblings' peerDependenci
npm ERR! peerinvalid Peer generator-angular@0.4.0 wants generator-karma@~0.5.0

npm ERR! System Windows_NT 6.1.7601
npm ERR! command "D:\\toolkit\\nodejs\\\\node.exe" "D:\\toolkit\\nodejs\\node_modules\\npm\\bi
lowercase_demo" "-g"
npm ERR! cwd D:\workspace\javascript\nodejs-package-project2
npm ERR! node -v v0.10.5
npm ERR! npm -v 1.2.19
npm ERR! code EPEERINVALID
npm ERR!
npm ERR! Additional logging details can be found in:
npm ERR!     D:\workspace\javascript\nodejs-package-project2\npm-debug.log
npm ERR! not ok code 0
```

错误不是lowercase_demo的，没有关系。

执行全局命令：lowercase

```{bash}
~ D:\workspace\javascript\nodejs-package-project2>lowercase test\data\sample.txt
D:\toolkit\nodejs\node_modules\lowercase_demo\bin/../test\data\sample.txt
java
nodejs
python
php
.net
r
ruby
c
c++
go
```

注： 关于命令lowercase，因为代码中定义的是相对目录，所以只能访问D:\toolkit\nodejs\node_modules\lowercase_demo\目录的数据文件。

这样，我们自定义的lowercase_demo库，开发完成，发布到npm官方依赖管理，并且安装成功！整体流程走了一遍发现还是挺简单的。

你也来动手试试吧！

#### 转载请注明出处：http://blog.fens.me/nodejs-npm-package


