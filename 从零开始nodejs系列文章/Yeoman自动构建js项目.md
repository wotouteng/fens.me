Yeoman自动构建js项目
==========

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-yeoman-intro/

![Yeoman自动构建js项目](http://blog.fens.me/wp-content/uploads/2013/08/yeoman.png)

#### 前言
有一种技术可以提高我们的工作效率，可以让我们专心做我们擅长的事，可以屏蔽复杂性，可以规范我们的架构和我们的代码，可以让我们的享受编程的乐趣。Yeoman可以做到。

很多年以前，rails刚刚出世，伴随着一个新的名词“脚手架(scaffolding)”出现了。脚手架是一种提高开发效率的工具的代名词。随后，各种编程语言都开始实现自己的“脚手架”工具。Maven重新构造了Java的世界，改变了几千万的开发者对于Java项目构建的认识。Yeoman在Javascript领域正做着同样的事情。

看着各种工具，各种语言，都在飞跃式的进化。做为一个开发者，我每天都在为变化而惊喜！

#### 目录

1. Yeoman介绍
2. Yeoman工具包yo命令
3. Yeoman工具包yo – 快速构建一个web项目
4. Yeoman工具包bower,grunt的组合用法

## 1. Yeoman介绍

Yeoman是Google的团队和外部贡献者团队合作开发的，他的目标是通过Grunt（一个用于开发任务自动化的命令行工具）和Bower（一个HTML、CSS、Javascript和图片等前端资源的包管理器）的包装为开发者创建一个易用的工作流。

Yeoman主要有三部分组成：yo（脚手架工具）、grunt（构建工具）、bower（包管理器）。这三个工具是分别独立开发的，但是需要配合使用，来实现我们高效的工作流模式。

+ yo：在本文中第二节会讲到
+ grunt：请参考 [grunt让Nodejs规范起来](http://blog.fens.me/nodejs-grunt-intro/)
+ bower：请参考 [bower解决js的依赖管理](http://blog.fens.me/nodejs-bower-intro/)

## 2. Yeoman工具包yo命令

yo插件都是通过npm, Node.js包管理器安装和管理的.

### 我的系统环境

+ win7 64bit
+ Nodejs:v0.10.5
+ Npm:1.2.19

```{bash}
~ D:\workspace\javascript>node -v
v0.10.5

~ D:\workspace\javascript>npm -v
1.2.19
```

在系统中，我们已经安装好了Nodejs和npm。win7安装nodejs请参考文章：[Nodejs开发框架Express3.0开发手记–从零开始](http://blog.fens.me/nodejs-express3/)

### 全局安装yo

```{bash}
~ D:\workspace\javascript>npm install -g yo
```

如果你还没有安装grunt,bower，也需要一起安装

```{bash}
~ D:\workspace\javascript>npm install -g grunt-cli bower
```

接下我们看一下yo命令行操作
### 1). 通过help查看帮助

```{bash}
~ D:\workspace\javascript>yo --help
Yeoman is a mask worn by the following members of the open-source community:

  Paul Irish, Addy Osmani, Mickael Daniel, Sindre Sorhus, Eric Bidelman,
  Frederick Ros, Brian Ford, Pascal Hartig, Stephen Sawchuk, and countless
  other contributors.

Usage: yo GENERATOR [args] [options]

General options:
  -h, --help     # Print generator's options and usage
  -f, --force    # Overwrite files that already exist

Please choose a generator below.
Angular
  angular:app
  angular:common
  angular:constant
  angular:controller
  angular:decorator
  angular:directive
  angular:factory
  angular:filter
  angular:main
  angular:provider
  angular:route
  angular:service
  angular:value
  angular:view

Karma
  karma:app

Mocha
  mocha:app
  mocha:generator

Webapp
  webapp:app
```

yo的命令很简单：yo GENERATOR，例如：yo webapp， yo angular
Please choose a generator below: 下面列出了我的系统中，已经安装的generator的库，例如：Angular，Karma，Mocha，Webapp

### 2). 查询yo的支持库

```{bash}

~ D:\workspace\javascript>yo
Yeoman is a mask worn by the following members of the open-source community:

  Paul Irish, Addy Osmani, Mickael Daniel, Sindre Sorhus, Eric Bidelman,
  Frederick Ros, Brian Ford, Pascal Hartig, Stephen Sawchuk, and countless
  other contributors.

[?] What would you like to do? (Use arrow keys)
 > Install a generator
   Find some help
   Get me out of here!
```

命令行会提示，我们想要的操作。这里选择”Install a generator”

```{bash}
[?] What would you like to do? Install a generator
[?] Search NPM for generators:web
```

系统继续提示，我们要查找的包，我们输入web

### 3). 安装generator-webapp库

```{bash}
[?] Here's what I found. Install one? (Use arrow keys)
 > generator-armadillo
   generator-bones
   generator-btapp
   generator-fe
   generator-flight
   generator-hazdev-webapp
   generator-hbswebapp
   generator-html5-site
   generator-jing
   generator-lessapp
   generator-nodestrap
   generator-sails
   generator-server-configs
   generator-starter
   generator-starttter
   generator-ultimate
   generator-webapp
   generator-webapp-bfytw
   generator-webapp-fintan
   generator-weblog
   generator-website
   Search again
   Return home
```

yo列出了，所有在官方已经注册的，web关键字相关的包，我们选择“generator-webapp”

yo通过npm开始下载generator-webapp安装包

```{bash}

[?] What would you like to do? Install a generator
[?] Search NPM for generators: web
[?] Here's what I found. Install one? generator-webapp

generator-webapp@0.2.7 D:\toolkit\nodejs\node_modules\generator-webapp
├── cheerio@0.12.1 (entities@0.3.0, underscore@1.4.4, htmlparser2@3.1.4, cheerio-select@0.0.3)
└── yeoman-generator@0.12.3 (dargs@0.1.0, diff@1.0.5, debug@0.7.2, async@0.2.9, mime@1.2.11, mkdirp@0.3.5, isbinaryfi
le@0.1.9, underscore.string@2.3.3, shelljs@0.1.4, iconv-lite@0.2.11, lodash@1.3.1, rimraf@2.1.4, nopt@2.1.2, cli-table@0
.2.0, tar@0.1.18, glob@3.2.6, inquirer@0.2.4, request@2.21.0)

I just installed your generator by running:
    npm install -g generator-webapp
```

然后，我们退出yo命令行，选择“Get me out of here!”

```{bash}

[?] What would you like to do? Get me out of here!

Bye from us! Chat soon.
            Addy Osmani
          Sindre Sorhus
        Brian Ford
     Eric Bidelman
              Paul Irish
     Mickael Daniel
          Pasca1 Hartig
      Stephen S.wchuk
    Frederick R0s
```

上面的退出的文字是开发者的名字，很有意思一点是，竖着看最中间一行的文字排列是，yeoman1.0，也就是yeoman这个项目的得名了。

### 4). 注：上面的过程，也可以直接用命令

```{bash}
npm install -g generator-webapp
```

## 3. Yeoman工具包yo – 快速构建一个web项目

当我们安装了generator-webapp后，我们就可以，以非常优雅的方式来构建工程了。

### 1). 创建nodejs-yo目录，并构建webapp工程

```{bash}
~ D:\workspace\javascript>mkdir nodejs-yo
~ D:\workspace\javascript>cd nodejs-yo
~ D:\workspace\javascript\nodejs-yo>yo webapp

     _-----_
    |       |
    |--(o)--|   .--------------------------.
   `---------´  |    Welcome to Yeoman,    |
    ( _´U`_ )   |   ladies and gentlemen!  |
    /___A___\   '__________________________'
     |  ~  |
   __'.___.'__
 ´   `  |° ´ Y `

Out of the box I include HTML5 Boilerplate, jQuery and Modernizr.
[?] What more would you like?
  [ ] Twitter Bootstrap for Sass
 >[X] RequireJS
  [X] Autoprefixer for your CSS

```

### 对选项的解释：

+ Twitter Bootstrap for Sass：SASS是一种CSS的开发工具，提供了许多便利的写法，大大节省了设计者的时间，使得CSS的开发，变得简单和可维护。
+ RequireJS 可以帮助用户异步按需的加载 JavaScript 代码，并解决 JavaScript 模块间的依赖关系，提升了前端代码的整体质量和性能。
+ Autoprefixer解析CSS文件并且添加浏览器前缀到CSS规则里，使用Can I Use的数据来决定哪些前缀是需要的。
+ 上面3个选择，由于Sass是基于Ruby的，我本机没有安装ruby，取消Twitter Bootstrap for Sass的勾选。

### 2). 查看安装日志：

```{bash}

Out of the box I include HTML5 Boilerplate, jQuery and Modernizr.
[?] What more would you like? RequireJS, Autoprefixer for your CSS
   create Gruntfile.js
   create package.json
   create .gitignore
   create .gitattributes
   create .bowerrc
   create bower.json
   create .jshintrc
   create .editorconfig
   create app/favicon.ico
   create app/404.html
   create app/robots.txt
   create app/.htaccess
   create app/styles/main.css
   create app/scripts/app.js
   create app/scripts/main.js
   create app/index.html
   create app/scripts/hello.coffee
   invoke   mocha:app
   create     test\index.html
   create     test\lib\chai.js
   create     test\lib\expect.js
   create     test\lib\mocha\mocha.css
   create     test\lib\mocha\mocha.js
   create     test\spec\test.js

I'm all done. Running bower install & npm install for you to install the required dependencies. If this fails, try running the command yourself.
```

项目骨架已经建好了。还包括了很多的配置文件.gitignore,bower.json,Gruntfile.js,favicon.ico, robots.txt, .htaccess。太神奇了，Cool!!

一下子装备都齐全了，打仗的事就交给我们自己吧。

## 4. Yeoman工具包bower,grunt的组合用法

有关bower的介绍，请参考：[bower解决js的依赖管理](http://blog.fens.me/nodejs-bower-intro/)
有关grunt的介绍，请参考：[grunt让Nodejs规范起来](http://blog.fens.me/nodejs-grunt-intro/)

### 1). 使用bower命令查看项目依赖：

```{bash}

~ D:\workspace\javascript\nodejs-yo>bower list
bower check-new     Checking for new versions of the project dependencies..
nodejs-yo#0.0.0 D:\workspace\javascript\nodejs-yo
├── jquery#1.9.1 (latest is 2.0.3)
├── modernizr#2.6.2
└── requirejs#2.1.8
```

我们发现生成的webapp中，有4个依赖库，jquery,modernizr,requirejs。

### 2). 使用grunt命令执行单元测试

```{bash}
D:\workspace\javascript\nodejs-yo>grunt test
Running "clean:server" (clean) task
Cleaning ".tmp"...OK

Running "concurrent:test" (concurrent) task

Running "copy:styles" (copy) task

Running "coffee:dist" (coffee) task

Running "autoprefixer:dist" (autoprefixer) task
File ".tmp/styles/main.css" created.

Running "connect:test" (connect) task
Starting connect web server on localhost:9000.

Running "mocha:all" (mocha) task
Testing: http://localhost:9000/index.html

  .

  1 test complete (107 ms)

>> 1 passed! (0.11s)

Done, without errors.
```

### 3). 使用grunt命令启动一个node服务器

```{bash}

~ D:\workspace\javascript\nodejs-yo>grunt server
Running "server" task

Running "clean:server" (clean) task

Running "concurrent:server" (concurrent) task
    Warning: Running "compass:dist" (compass) task
Warning: You need to have Ruby and Compass installed and in your system PATH for this task to work. More info: https://g
ithub.com/gruntjs/grunt-contrib-compass Use --force to continue. Use --force to continue.

    Aborted due to warnings.
```

启动时，我们发现报错了。由于我没有装Ruby的环境，所以compass:dist的命令不能运行。

强制启动grunt

```{bash}
~ D:\workspace\javascript\nodejs-yo>grunt server --force
D:\workspace\javascript\nodejs-yo>grunt server --force
Running "server" task

Running "clean:server" (clean) task
Cleaning ".tmp"...OK

Running "concurrent:server" (concurrent) task

Running "coffee:dist" (coffee) task
    Warning: Running "compass:dist" (compass) task
Warning: You need to have Ruby and Compass installed and in your system PATH for this task to work. More info: https://
ithub.com/gruntjs/grunt-contrib-compass Use --force to continue. Used --force, continuing.

Running "compass:dist" (compass) task
Warning: You need to have Ruby and Compass installed and in your system PATH for this task to work. More info: https://
ithub.com/gruntjs/grunt-contrib-compass Use --force to continue.

Running "copy:styles" (copy) task

Running "autoprefixer:dist" (autoprefixer) task
File ".tmp/styles/main.css" created.

Running "connect:livereload" (connect) task
Starting connect web server on localhost:9000.

Running "open:server" (open) task

Running "watch" task
Waiting...
```

后台日志显示了“watch”的task，说明代码文件被监控着，如有修改网页会自动更新。

浏览器被自动打开，http://localhost:9000/

![](http://blog.fens.me/wp-content/uploads/2013/08/yeoman1.png)

### 4). 修改app/index.html代码，增加对canvas的检查。

```{bash}
~ vi app/index.html

<script>
window.onload = function () {
if (canvasSupported()) {
alert('canvas supported');
}
};

function canvasSupported() {
var canvas = document.createElement('canvas');
return (canvas.getContext && canvas.getContext('2d'));
}
</script>
```

浏览器显示：

![](http://blog.fens.me/wp-content/uploads/2013/08/yeoman2.png)

### 5). 通过bower增加库jquery-pjax

```{bash}
~ D:\workspace\javascript\nodejs-yo>bower install jquery-pjax
bower jquery-pjax#*         not-cached git://github.com/defunkt/jquery-pjax.git#*
bower jquery-pjax#*            resolve git://github.com/defunkt/jquery-pjax.git#*
bower jquery-pjax#*           download https://github.com/defunkt/jquery-pjax/archive/v1.7.3.tar.gz
bower jquery-pjax#*            extract archive.tar.gz
bower jquery-pjax#*           resolved git://github.com/defunkt/jquery-pjax.git#1.7.3
bower jquery#>=1.8              cached git://github.com/components/jquery.git#2.0.3
bower jquery#>=1.8            validate 2.0.3 against git://github.com/components/jquery.git#>=1.8
bower jquery-pjax#~1.7.3       install jquery-pjax#1.7.3

jquery-pjax#1.7.3 app\bower_components\jquery-pjax
└── jquery#2.0.3

~ D:\workspace\javascript\nodejs-yo>bower list
bower check-new     Checking for new versions of the project dependencies..
nodejs-yo#0.0.0 D:\workspace\javascript\nodejs-yo
├── jquery#1.9.1 (latest is 2.0.3)
├─┬ jquery-pjax#1.7.3 extraneous
│ └── jquery#1.9.1 (2.0.3 available)
├── modernizr#2.6.2
└── requirejs#2.1.8
```

Yeoman已经成功地帮助我们打造出一个项目的原型了，接下来我们可以针对自己的项目，自己熟悉的框架组合，构建一个属于自己的骨架模板，并发布到Yeoman的generator库中。

后面的内容会更精彩，敬请期待…！！

#### 转载请注明出处：http://blog.fens.me/nodejs-yeoman-intro/

