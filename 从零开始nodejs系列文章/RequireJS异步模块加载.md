RequireJS异步模块加载
==========

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-requirejs/

![](http://blog.fens.me/wp-content/uploads/2013/09/requirejs.png)

#### 前言

随着项目越来越大，依赖越来越多，代码的耦合度也越来越高。我们需要从架构的层面设计和优化代码的组织结构。RequireJS遵循AMD(异步模块定义)规范，从架构层抽象出“模块化”开发方案，并以标准化了模块化开发，同时和其他的开发框架保持兼容。

按照RequireJS的规范，我们能够更容易地实现更复杂，更强大的JS的富客户端程序。

#### 目录

1. RequireJS介绍
2. RequireJS安装
3. RequireJS基本使用
4. nodejs构建简易的web服务器
5. RequireJS模块化
6. 多路径配置: baseUrl,paths
7. 编译Requirejs模块

## 1. RequireJS介绍

RequireJS是一个Javascript的模块加载器，倡导的是一种模块化开发理念，核心价值是让 JavaScript 的模块化开发变得更简单自然。RequireJS 遵循的是 AMD（异步模块定义）规范，帮助用户异步按需的加载 JavaScript 代码，并解决 JavaScript 模块间的依赖关系，提升了前端代码的整体质量和性能。

## 2. RequireJS安装

### 我的系统环境

* win7 64bit
* Nodejs:v0.10.5
* Npm:1.2.19

通过nodejs安装RequireJS

```{bash}

~ D:\workspace\javascript>mkdir nodejs-require && cd nodejs-require
~ D:\workspace\javascript\nodejs-require>npm install requirejs
npm http GET https://registry.npmjs.org/requirejs
npm http 200 https://registry.npmjs.org/requirejs
requirejs@2.1.8 node_modules\requirejs
```

全局安装requirejs：使用r.js工具。

```{bash}

~ D:\workspace\javascript\nodejs-require>npm install requirejs -g
npm http GET https://registry.npmjs.org/requirejs
npm http 304 https://registry.npmjs.org/requirejs
D:\toolkit\nodejs\r.js -> D:\toolkit\nodejs\node_modules\requirejs\bin\r.js
npm ERR! peerinvalid The package generator-karma does not satisfy its siblings' peerDependencies requirements!
npm ERR! peerinvalid Peer generator-angular@0.4.0 wants generator-karma@~0.5.0

npm ERR! System Windows_NT 6.1.7601
npm ERR! command "D:\\toolkit\\nodejs\\\\node.exe" "D:\\toolkit\\nodejs\\node_modules\\npm\\bin\\npm-cli.js" "install" "
requirejs" "-g"
npm ERR! cwd D:\workspace\javascript\nodejs-require
npm ERR! node -v v0.10.5
npm ERR! npm -v 1.2.19
npm ERR! code EPEERINVALID
npm ERR!
npm ERR! Additional logging details can be found in:
npm ERR!     D:\workspace\javascript\nodejs-require\npm-debug.log
npm ERR! not ok code 0
```

运行r.js命令失败

```{bash}
~ D:\workspace\javascript\nodejs-require>r.js
```

![](http://blog.fens.me/wp-content/uploads/2013/09/requirejs-rjs.png)

我的win7环境中有错误，所以我只能在当前项目中运行r.js命令.(Linux下面可以全局安装。)

```{bash}
~ D:\workspace\javascript\nodejs-require>node node_modules\requirejs\bin\r.js -h
See https://github.com/jrburke/r.js for usage.
```

## 3. RequireJS基本使用

创建项目文件：

```{bash}

~ D:\workspace\javascript\nodejs-require>ls -l
-rwx------  1 4294967295 mkpasswd  65 Sep 19 13:32 a.js
-rwx------  1 4294967295 mkpasswd  59 Sep 18 20:33 b.js
-rwx------  1 4294967295 mkpasswd  82 Sep 18 20:33 c.js
-rwx------  1 4294967295 mkpasswd  69 Sep 18 20:33 d.js
-rwx------  1 4294967295 mkpasswd 206 Sep 19 13:32 index.html
-rwx------  1 4294967295 mkpasswd  48 Sep 19 13:31 main.js
drwx------+ 1 4294967295 mkpasswd   0 Sep 18 20:32 node_modules
```

对文件的定义：

* index.html: 用于加载javascript文件，这里只加载RequireJS库
* main.js: RequireJS的模块组，用来封装JS模块，通过js来加载其他的js文件
* a.js: 一个JS的功能文件，不依赖其他库
* b.js: 一个JS的功能文件，不依赖其他库
* c.js: 一个JS的功能文件，依赖a.js和b.js
* d.js: 一个JS的功能文件，依赖c.js

新增文件：index.html

```{bash}

<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8" />
<title>RequireJS</title>
<script data-main="main" src="node_modules/requirejs/require.js"></script>
</head>
<h1>RequireJS Testing</h1>
<body>
</body>
</html>
```

新增文件：main.js

```{bash}
require(['a'],function(){
     a();
})
```

新增文件：a.js

```{bash}
function a(){
    console.log("aaaaaaaaaaaaaaaaaaaaaaaaaaa");
}
```

新增文件：b.js

```{bash}
function b(){
    console.log("bbbbbbbbbbbbbbbbbbbbb");
}
```

新增文件：c.js

```{bash}
function c(){
    a();
    b();
    console.log("cccccccccccccccccccccccc");
}
```

新增文件：d.js

```{bash}

function d(){
    c();
    console.log("ddddddddddddddddddddd");
}
```

打开浏览器：file:///D:/workspace/javascript/nodejs-require/index.html

![](http://blog.fens.me/wp-content/uploads/2013/09/requirejs-testing1.png)

我们发现a.js被加载了，但我们并没有在index.html中定义script加载a.js。

1. a.js的加载是RequireJS帮我们做的。
2. 通过在HTML中定义data-main属性，注册一个模块main.js。
3. 在main.js中，通过声明式定义，引入a.js文件
4. 实现了对a.js加载的控制

通过require函数完全成了依赖管理的功能

```{bash}
require(dependencies, callback)
```

* dependencies: 是我们要载入的js，使用相对路径。
* callback: 是封装有程序逻辑。

所以，我们可以发现了RequireJS可以帮我们做，js依赖管理。我们可以再也不用在HTML中，以很土的加载顺序的方式管理JS文件了。这种优雅的注入可以帮我很好的管理全局变量。

## 4. nodejs构建简易的web服务器

在进行下一步之前，我们要用构建一个简易的web服务器，RequireJS的模块化加载需要web server的支持，通过本地文件的方式是不行的。

安装connect依赖

```{bash}
~ D:\workspace\javascript\nodejs-require>npm install connect
connect@2.9.0 node_modules\connect
├── uid2@0.0.2
├── methods@0.0.1
├── cookie-signature@1.0.1
├── pause@0.0.1
├── fresh@0.2.0
├── bytes@0.2.0
├── qs@0.6.5
├── buffer-crc32@0.2.1
├── cookie@0.1.0
├── debug@0.7.2
├── send@0.1.4 (range-parser@0.0.4, mime@1.2.11)
└── multiparty@2.1.8 (stream-counter@0.1.0, readable-stream@1.0.17)
```

新建文件：app.js

```{bash}
~ vi app.js

var connect = require('connect');
connect.createServer(
    connect.static(__dirname)
).listen(3000);
```

开发模式启动服务器:

```{bash}
supervisor app.js
```

打开浏览器：http://localhost:3000

![](http://blog.fens.me/wp-content/uploads/2013/09/requirejs-testing2.png)

## 5. RequireJS模块化

虽然我们可以把所有的程序都写在main.js的callback中，但是程序会变得复杂，关系也不清楚。接下来，我们要做模块化的编程。RequireJS也实现了AMD定义的define的API。

```{bash}
define(id?, dependencies?, factory)
```

* id:字符串，模块名称，可选。
* dependencies: 是我们要载入的js，使用相对路径。
* factory: 工厂方法，返回一个模块函数

新建ab.js，实现模块ab：

```{bash}

~ vi ab.js

define(function(){
    return {
        a: function(){
            a();
            console.log("ab.a()");
        },
        b:function(){
            b();
            console.log("ab.b()");
        }
    };
});
```

修改main.js

```{bash}
~ vi main.js

require(['a','b','ab'],function(a,b,ab){
    ab.b();
})
```

刷新页面：http://localhost:3000/

![](http://blog.fens.me/wp-content/uploads/2013/09/requirejs-testing3.png)

调用流程：

1. 通过main.js加载了a.js,b.js,ab.js文件
2. ab.js构建一个ab的模块
3. 在ab的模块中，调用b.js的b()函数

通过模块化的封装后，我们可以更优化我们的程序，程序结构更清晰！

## 6. 多路径配置: baseUrl,paths

如果我们需要加载多个js文件时，文件在不同的目录下面，我们可以通过baseUrl和paths来设置路径的默认位置和路径别名，方便我们定位文件。

```{bash}

#新建目录
~ mkdir folder
~ mkdir folder/f1
~ mkdir folder/f2

#把c.js移动到f1
~ mv c.js folder/f1

#把d.js移动到f2
~ mv d.js folder/f2
```

修改main.js

```{bash}
~ vi main.js
require.config({
    baseUrl:'./',
    paths:{
        f1:'folder/f1',
        f2:'folder/f2'
    }
});

require([
    'b',
    'a',
    'ab',
    'f1/c',
    'f2/d'
],function(a,b,ab,c){
    d();
})
```

刷新浏览器：

![](http://blog.fens.me/wp-content/uploads/2013/09/requirejs-testing4.png)

我们看到c.js, d.js都被正确的加载了。

## 7. 编译Requirejs模块

最后要讲的是，通过r.js命令，对模块进行翻译优化。

在RequireJS安装部分，我们没有实现全局安装，因些只能在项目中通过相对路径使用r.js的命令。

```{bash}

~ D:\workspace\javascript\nodejs-require>node node_modules\requirejs\bin\r.js -o name=main out=main-build.js baseUrl=. pat
hs.f1="folder/f1" paths.f2="folder/f2"

Tracing dependencies for: main
Uglifying file: D:/workspace/javascript/nodejs-require/main-build.js

D:/workspace/javascript/nodejs-require/main-build.js
----------------
D:/workspace/javascript/nodejs-require/b.js
D:/workspace/javascript/nodejs-require/a.js
D:/workspace/javascript/nodejs-require/ab.js
D:/workspace/javascript/nodejs-require/folder/f1/c.js
D:/workspace/javascript/nodejs-require/folder/f2/d.js
D:/workspace/javascript/nodejs-require/main.js
```

我们会发现，新生成一个文件main-build.js

```{bash}
~ cat main-build.js

function b(){console.log("bbbbbbbbbbbbbbbbbbbbb")}function a(){console.log("aaaaaaaaaaaaaaaaaaaaaaaaaaa")}function c(){a(),b(),console.log("cccccccccccccccccccccccc")}function d(){c(),console.log("ddddddddddddddddddddd")}define("b",function(){}),define("a",function(){}),define("ab",[],function(){return{a:function(){a(),console.log("ab.a()")},b:function(){b(),console.log("ab.b()")}}}),define("f1/c",function(){}),define("f2/d",function(){}),require.config({baseUrl:"./",paths:{f1:"folder/f1",f2:"folder/f2"}}),require(["b","a","ab","f1/c","f2/d"],function(e,t,n,r){d()}),define("main",function(){});
```

新生成的main-build.js可以用来做为发布的功能模块。

本文简单的介绍了RequireJS的使用，如果我们的程序按照AMD的模块化思路去构建，我相信JS的代码，也是健壮的，可维护的，可传递的。

#### 转载请注明出处：http://blog.fens.me/nodejs-requirejs/

