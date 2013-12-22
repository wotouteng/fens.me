bower解决js的依赖管理
====================

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-bower-intro/

![bower解决js的依赖管理](http://blog.fens.me/wp-content/uploads/2013/08/bower.png)

#### 前言
一个新的web项目开始，我们总是很自然地去下载需要用到的js类库文件，比如jQuery，去官网下载名为jquery-1.10.2.min.js文件，放到我们的项目里。当项目又需要bootstrap的时候，我们会重复刚才的工作，去bootstrap官网下载对应的类库。如果bootstrap所依赖的jQuery并不是1.10.2，而是2.0.3时，我们会再重新下载一个对应版本的jQuery替换原来的。

包管理是个复杂的问题，我们要知道谁依赖谁，还要明确哪个版本依赖哪个版本。这些对于开发人员来说，负担过重了。bower作为一个js依赖管理的工具，提供一种理想包管理方式，借助了npm的一些思想，为我们提供一个舒服的开发环境。

你要还不动起手来试试bower，那你一定不会知道，前端开发是件多么享受的事。

#### 目录

1. bower介绍
2. bower安装
3. bower命令
4. bower使用
5. 用bower提交自己类库

## 1. bower介绍

Bower 是 twitter 推出的一款包管理工具，基于nodejs的模块化思想，把功能分散到各个模块中，让模块和模块之间存在联系，通过 Bower 来管理模块间的这种联系。

包管理工具一般有以下的功能：

+ 注册机制：每个包需要确定一个唯一的 ID 使得搜索和下载的时候能够正确匹配，所以包管理工具需要维护注册信息，可以依赖其他平台。
+ 文件存储：确定文件存放的位置，下载的时候可以找到，当然这个地址在网络上是可访问的。
+ 上传下载：这是工具的主要功能，能提高包使用的便利性。比如想用 jquery 只需要 install 一下就可以了，不用到处找下载。上传并不是必备的，根据文件存储的位置而定，但需要有一定的机制保障。
+ 依赖分析：这也是包管理工具主要解决的问题之一，既然包之间是有联系的，那么下载的时候就需要处理他们之间的依赖。下载一个包的时候也需要下载依赖的包。

功能介绍，摘自文章：http://chuo.me/2013/02/twitter-bower.html

## 2. bower安装

bower插件是通过npm, Node.js包管理器安装和管理的.

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

### 安装bower 

全局安装bower

```{bash}

~ D:\workspace\javascript>npm install bower -g
```

新建一个express3的项目nodejs-bower

```{bash}
~ D:\workspace\javascript>express -e nodejs-bower
~ D:\workspace\javascript>cd nodejs-bower && npm install
```

## 3. bower命令

bower安装后，我们就可以用bower这个命令了。

```{bash}
~ D:\workspace\javascript\nodejs-bower>bower
Usage:
    bower  [] []
Commands:
    cache                   Manage bower cache
    help                    Display help information about Bower
    home                    Opens a package homepage into your favorite browser
    info                    Info of a particular package
    init                    Interactively create a bower.json file
    install                 Install a package locally
    link                    Symlink a package folder
    list                    List local packages
    lookup                  Look up a package URL by name
    prune                   Removes local extraneous packages
    register                Register a package
    search                  Search for a package by name
    update                  Update a local package
    uninstall               Remove a local package
Options:
    -f, --force             Makes various commands more forceful
    -j, --json              Output consumable JSON
    -l, --log-level         What level of logs to report
    -o, --offline           Do not hit the network
    -q, --quiet             Only output important information
    -s, --silent            Do not output anything, besides errors
    -V, --verbose           Makes output more verbose
    --allow-root            Allows running commands as root
See 'bower help ' for more information on a specific command.
```

Commands，列出了bower支持的各种命令。

+ cache:bower缓存管理
+ help:显示Bower命令的帮助信息
+ home:通过浏览器打开一个包的github发布页
+ info:查看包的信息
+ init:创建bower.json文件
+ install:安装包到项目
+ link:在本地bower库建立一个项目链接
+ list:列出项目已安装的包
+ lookup:根据包名查询包的URL
+ prune:删除项目无关的包
+ register:注册一个包
+ search:搜索包
+ update:更新项目的包
+ uninstall:删除项目的包

## 4. bower使用

### 1). 安装jQuery到项目nodejs-bower

```{bash}
~ D:\workspace\javascript\nodejs-bower>bower install jquery
bower jquery#*              not-cached git://github.com/components/jquery.git#*
bower jquery#*                 resolve git://github.com/components/jquery.git#*
bower jquery#*                download https://github.com/components/jquery/archive/2.0.3.tar.gz
bower jquery#*                 extract archive.tar.gz
bower jquery#*                resolved git://github.com/components/jquery.git#2.0.3
bower jquery#~2.0.3            install jquery#2.0.3

jquery#2.0.3 bower_components\jquery
```

通过执行命令，我们可以看到jQuery的最新版本被下载，并安装到项目的bower_components\jquery目录

查看bower_components/jquery目录，发现了3个文件。

```{bash}

~ D:\workspace\javascript\nodejs-bower>ls bower_components/jquery -a
.  ..  .bower.json  component.json  jquery.js
```

同样地，我们的项目还需要d3的类库

```{bash}
~ D:\workspace\javascript\nodejs-bower>bower install d3
bower d3#*                  not-cached git://github.com/mbostock/d3.git#*
bower d3#*                     resolve git://github.com/mbostock/d3.git#*
bower d3#*                    download https://github.com/mbostock/d3/archive/v3.2.8.tar.gz
bower d3#*                     extract archive.tar.gz
bower d3#*                    resolved git://github.com/mbostock/d3.git#3.2.8
bower d3#~3.2.8                install d3#3.2.8

d3#3.2.8 bower_components\d3
```

非常方便，下载并安装完成！

### 2). 查看项目中已导入的类库

```{bash}

~ D:\workspace\javascript\nodejs-bower>bower list
bower check-new     Checking for new versions of the project dependencies..
nodejs-bower#0.0.0 D:\workspace\javascript\nodejs-bower
├── d3#3.2.8
└── jquery#2.0.3
```

### 3). 安装bootstrap库，并查看依赖情况

```{bash}
~ D:\workspace\javascript\nodejs-bower>bower install bootstrap
bower bootstrap#*               cached git://github.com/twbs/bootstrap.git#3.0.0-rc1
bower bootstrap#*             validate 3.0.0-rc1 against git://github.com/twbs/bootstrap.git#*
bower jquery#>= 1.9.0           cached git://github.com/components/jquery.git#2.0.3
bower jquery#>= 1.9.0         validate 2.0.3 against git://github.com/components/jquery.git#>= 1.9.0
bower bootstrap#~3.0.0-rc1     install bootstrap#3.0.0-rc1

bootstrap#3.0.0-rc1 bower_components\bootstrap
└── jquery#2.0.3

~ D:\workspace\javascript\nodejs-bower>bower list
bower check-new     Checking for new versions of the project dependencies..
nodejs-bower#0.0.0 D:\workspace\javascript\nodejs-bower
├─┬ bootstrap#3.0.0-rc1 extraneous
│ └── jquery#2.0.3
├── d3#3.2.8
└── jquery#2.0.3
```

我们发现bootstrap，对jquery是有依赖的。

### 4). 删除jQuery库，破坏依赖关系

```{bash}

~ D:\workspace\javascript\nodejs-bower>bower uninstall jquery
bower conflict      bootstrap depends on jquery
Continue anyway? (y/n) y
bower uninstall     jquery

~ D:\workspace\javascript\nodejs-bower>bower list
bower check-new     Checking for new versions of the project dependencies..
nodejs-bower#0.0.0 D:\workspace\javascript\nodejs-bower
├─┬ bootstrap#3.0.0-rc1 extraneous
│ └── jquery missing
├── d3#3.2.8
└── jquery missing
```

5). 安装低版本的jQuery，制造不版本兼容

```{bash}
~ D:\workspace\javascript\nodejs-bower>bower install jquery#1.7.2
bower jquery#~2.0.3             cached git://github.com/components/jquery.git#2.0.3
bower jquery#~2.0.3           validate 2.0.3 against git://github.com/components/jquery.git#~2.0.3
bower jquery#>= 1.9.0           cached git://github.com/components/jquery.git#2.0.3
bower jquery#>= 1.9.0         validate 2.0.3 against git://github.com/components/jquery.git#>= 1.9.0
bower jquery#1.7.2              cached git://github.com/components/jquery.git#1.7.2
bower jquery#1.7.2            validate 1.7.2 against git://github.com/components/jquery.git#1.7.2

Unable to find a suitable version for jquery, please choose one:
    1) jquery#1.7.2 which resolved to 1.7.2
    2) jquery#~2.0.3 which resolved to 2.0.3 and has nodejs-bower as dependants
    3) jquery#>= 1.9.0 which resolved to 2.0.3 and has bootstrap#3.0.0-rc1 as dependants

Prefix the choice with ! to persist it to bower.json

Choice: 1
bower jquery#1.7.2             install jquery#1.7.2

jquery#1.7.2 bower_components\jquery

~ D:\workspace\javascript\nodejs-bower>bower list
bower check-new     Checking for new versions of the project dependencies..
nodejs-bower#0.0.0 D:\workspace\javascript\nodejs-bower
├─┬ bootstrap#3.0.0-rc1 extraneous
│ └── jquery#1.7.2 incompatible with >= 1.9.0 (2.0.3 available)
├── d3#3.2.8
└── jquery#1.7.2 incompatible with ~2.0.3 (2.0.3 available)

```

我们可以清楚的看到bower，很明确的告诉了我们，jquery和bootstrap是不兼容的，强大之处大家应该有所体会。

### 6).升级jQuery，让版本兼容

```{bash}
~ D:\workspace\javascript\nodejs-bower>bower update jquery
bower jquery#~2.0.3             cached git://github.com/components/jquery.git#2.0.3
bower jquery#~2.0.3           validate 2.0.3 against git://github.com/components/jquery.git#~2.0.3
bower jquery#>= 1.9.0           cached git://github.com/components/jquery.git#2.0.3
bower jquery#>= 1.9.0         validate 2.0.3 against git://github.com/components/jquery.git#>= 1.9.0
bower jquery#~2.0.3            install jquery#2.0.3

jquery#2.0.3 bower_components\jquery

~ D:\workspace\javascript\nodejs-bower>bower list
bower check-new     Checking for new versions of the project dependencies..
nodejs-bower#0.0.0 D:\workspace\javascript\nodejs-bower
├─┬ bootstrap#3.0.0-rc1 extraneous
│ └── jquery#2.0.3
├── d3#3.2.8
└── jquery#2.0.3
```

多么智能，一键搞定，这才是高效的开发。

### 7). 查看本地bower已经缓存的类库

```{bash}
~ D:\workspace\javascript\nodejs-bower>bower cache list
bootstrap=git://github.com/twbs/bootstrap.git#3.0.0-rc1
d3=git://github.com/mbostock/d3.git#3.2.8
jquery=git://github.com/components/jquery.git#1.7.2
jquery=git://github.com/components/jquery.git#2.0.3
```

### 8). 查看D3库信息

```{bash}
~ D:\workspace\javascript\nodejs-bower>bower info d3
d3

  Versions:
    - 3.2.8
    - 3.2.7
    - 3.2.6
    - 3.2.5
    - 3.2.4
    - 3.2.3
    ...
```

### 9). 查看dojo库的url

```{bash}
~ D:\workspace\javascript\nodejs-bower>bower lookup dojo
dojo git://github.com/dojo/dojo.git
```

### 10). 用浏览器打开dojo的发布主页

```{bash}
~ D:\workspace\javascript\nodejs-bower>bower home dojo
bower dojo#*                not-cached git://github.com/dojo/dojo.git#*
bower dojo#*                   resolve git://github.com/dojo/dojo.git#*
bower dojo#*                  download https://github.com/dojo/dojo/archive/1.9.1.tar.gz
bower dojo#*                   extract archive.tar.gz
bower dojo#*                  resolved git://github.com/dojo/dojo.git#1.9.1
```

浏览器自动打开：https://github.com/dojo/dojo

### 11). 查询包含dojo的类库

```{bash}
~ D:\workspace\javascript\nodejs-bower>bower search dojo
Search results:

    dojo git://github.com/dojo/dojo.git
    dojox git://github.com/dojo/dojox.git
    dojo-util git://github.com/dojo/util.git
    dojo-bootstrap git://github.com/samvdb/Dojo-Bootstrap
```

真是方便实用的技术。

## 5. 用bower提交自己类库

### 1). 生成bower.json配置文件

```{bash}
~ D:\workspace\javascript\nodejs-bower>bower init
bower existing      The existing bower.json file will be used and filled in
[?] name: nodejs-bower
[?] version: 0.0.0
[?] main file:
[?] set currently installed components as dependencies? No
[?] add commonly ignored files to ignore list? Yes
```

查看生成的文件bower.json

```{bash}

{
  "name": "nodejs-bower",
  "version": "0.0.0",
  "ignore": [
    "**/.*",
    "node_modules",
    "bower_components",
    "test",
    "tests"
  ],
  "dependencies": {
    "d3": "git://github.com/mbostock/d3.git#~3.2.8",
    "jquery": "git://github.com/components/jquery.git#~2.0.3"
  }
}
```

### 2). 在github创建一个资源库：nodejs-bower
### 3). 本地工程绑定github

```{bash}

~ D:\workspace\javascript\nodejs-bower>git init
Initialized empty Git repository in D:/workspace/javascript/nodejs-bower/.git/

~ D:\workspace\javascript\nodejs-bower>git add .
~ D:\workspace\javascript\nodejs-bower>git commit -m "first commit"
# On branch master
#
# Initial commit
#
# Untracked files:
#   (use "git add ..." to include in what will be committed)
#
#       app.js
#       bower.json
#       bower_components/
#       node_modules/
#       package.json
#       public/
#       routes/
#       views/
nothing added to commit but untracked files present (use "git add" to track)

~ D:\workspace\javascript\nodejs-bower>git remote add origin https://github.com/bsspirit/nodejs-bower

~ D:\workspace\javascript\nodejs-bower>git push -u origin master
Counting objects: 565, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (516/516), done.
Writing objects: 100% (565/565), 803.08 KiB, done.
Total 565 (delta 26), reused 0 (delta 0)
To https://github.com/bsspirit/nodejs-bower
 * [new branch]      master -> master
Branch master set up to track remote branch master from origin.
```

### 4). 注册到bower官方类库

```{bash}
~ D:\workspace\javascript\nodejs-bower>bower register nodejs-bower git@github.com:bsspirit/nodejs-bower.git
bower                          convert Converted git@github.com:bsspirit/nodejs-bower.git to git://github.com/bsspirit/n
odejs-bower.git
bower nodejs-bower#*           resolve git://github.com/bsspirit/nodejs-bower.git#*
bower nodejs-bower#*          checkout master
bower nodejs-bower#*          resolved git://github.com/bsspirit/nodejs-bower.git#af3ceaac07
Registering a package will make it visible and installable via the registry (https://bower.herokuapp.com), continue? (y/
n)                    y
bower nodejs-bower            register git://github.com/bsspirit/nodejs-bower.git

Package nodejs-bower registered successfully!
All valid semver tags on git://github.com/bsspirit/nodejs-bower.git will be available as versions.
To publish a new version, you just need release a valid semver tag.

Run bower info nodejs-bower to list the package versions.
```

### 5). 查询我们自己的包

```{bash}
D:\workspace\javascript\nodejs-bower>bower search nodejs-bower
Search results:

    nodejs-bower git://github.com/bsspirit/nodejs-bower.git
```

### 6). 安装我们自己的包

```{bash}
D:\workspace\javascript\nodejs-bower>bower install nodejs-bower
bower nodejs-bower#*            cached git://github.com/bsspirit/nodejs-bower.git#af3ceaac07
bower nodejs-bower#*          validate af3ceaac07 against git://github.com/bsspirit/nodejs-bower.git#*
bower nodejs-bower#*           install nodejs-bower#af3ceaac07

nodejs-bower#af3ceaac07 bower_components\nodejs-bower
├── d3#3.2.8
└── jquery#2.0.3
```

其实模块化，版本依赖，开发类库，发布类库，安装类库，都是一条命令！还能再简单一点么！快把项目模块化，然后分享给大家吧！！未来是属于开发者的。

#### 转载请注明出处：http://blog.fens.me/nodejs-bower-intro/


