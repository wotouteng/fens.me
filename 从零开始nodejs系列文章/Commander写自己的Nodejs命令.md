Commander写自己的Nodejs命令
=============

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-commander/

![Commander写自己的Nodejs命令](http://blog.fens.me/wp-content/uploads/2013/09/commander.png)

#### 前言

在使用Nodejs过程中，有很多包都支持全局安装，然后提供一个命令，然后在命令行我们就可以完成一些任务，像 [express](http://blog.fens.me/nodejs-express3/), [grunt](http://blog.fens.me/nodejs-grunt-intro/), [bower](http://blog.fens.me/nodejs-bower-intro/), [yeoman](http://blog.fens.me/nodejs-yeoman-intro/), [reap](http://blog.fens.me/nodejs-gc-reap/), [karma](http://blog.fens.me/nodejs-karma-jasmine/), [requirejs](http://blog.fens.me/nodejs-requirejs/) 等。有时候，我们也需要自己开发这样的命令行工具。

commander.js，可以帮助我们简化命令行的开发。

#### 目录

1. commander介绍
2. commander安装
3. commander的API
4. 开发自定义的命令
5. 发布为运行命令

## 1. commander介绍

commander是一个轻巧的nodejs模块，提供了用户命令行输入和参数解析强大功能。commander源自一个同名的Ruby项目。

### commander的特性：

* 自记录代码
* 自动生成帮助
* 合并短参数（“ABC”==“-A-B-C”）
* 默认选项
* 强制选项​​
* 命令解析
* 提示符

## 2. commander安装

### 我的系统环境

* win7 64bit
* Nodejs:v0.10.5
* Npm:1.2.19

安装commander

```{bash}
~ D:\workspace\javascript>mkdir nodejs-commander && cd nodejs-commander
~ D:\workspace\javascript\nodejs-commander>npm install commander
npm http GET https://registry.npmjs.org/commander
npm http 304 https://registry.npmjs.org/commander
commander@2.0.0 node_modules\commander
```

编写一个简单的例子：增加文件app.js

```{bash}
~ vi app.js

var program = require('commander');

program
    .version('0.0.1')
    .option('-p, --peppers', 'Add peppers')
    .option('-P, --pineapple', 'Add pineapple')
    .option('-b, --bbq', 'Add bbq sauce')
    .option('-c, --cheese [type]', 'Add the specified type of cheese [marble]', 'marble')
    .parse(process.argv);

console.log('you ordered a pizza with:');
if (program.peppers) console.log('  - peppers');
if (program.pineapple) console.log('  - pineapple');
if (program.bbq) console.log('  - bbq');
console.log('  - %s cheese', program.cheese);
```

命令行输入测试:

### 无参数

```{bash}
~ D:\workspace\javascript\nodejs-commander>node app.js
you ordered a pizza with:
  - marble cheese
```

### 一个参数

```{bash}
~ D:\workspace\javascript\nodejs-commander>node app.js -c
you ordered a pizza with:
  - marble cheese
```

### 多个参数

```{bash}
~ D:\workspace\javascript\nodejs-commander>node app.js -cbp
you ordered a pizza with:
  - peppers
  - bbq
  - marble cheese
```

### help

```{bash}
~ D:\workspace\javascript\nodejs-commander>node app.js --help

  Usage: app.js [options]
  Options:
    -h, --help           output usage information
    -V, --version        output the version number
    -p, --peppers        Add peppers
    -P, --pineapple      Add pineapple
    -b, --bbq            Add bbq sauce
    -c, --cheese [type]  Add the specified type of cheese [marble]
```

### Version

```{bash}
~ D:\workspace\javascript\nodejs-commander>node app.js -V
0.0.1
```

## 3. commander的API

* Option(): 初始化自定义参数对象，设置“关键字”和“描述”
* Command(): 初始化命令行参数对象，直接获得命令行输入
* Command#command(): 定义一个命令名字
* Command#action(): 注册一个callback函数
* Command#option(): 定义参数，需要设置“关键字”和“描述”，关键字包括“简写”和“全写”两部分，以”,”,”|”,”空格”做分隔。
* Command#parse(): 解析命令行参数argv
* Command#description(): 设置description值
* Command#usage(): 设置usage值

请参考API的官方例子：https://github.com/visionmedia/commander.js/tree/master/examples

## 4. 开发自定义的命令

需求描述：设计一个服务器启动命令，包括自命令：指定部署节点，配置选择：部署目录，部署配置文件，允许的IP列表，出始种子值，计算的阈值区间。

### 新建文件：myServer.js

```{bash}
~ vi myServer.js

var program = require('commander');

function range(val) {
return val.split('..').map(Number);
}

function list(val) {
return val.split(',');
}

program
.version('0.0.1')
.usage('test')
.option('-C, --chdir [value]', '设置服务器节点','/home/conan/server')
.option('-c, --config [value]', '设置配置文件','./deploy.conf')
.option('-m, --max <n>', '最大连接数', parseInt)
.option('-s, --seed <n>', '出始种子', parseFloat)
.option('-r, --range <a>..<b>', '阈值区间', range)
.option('-l, --list <items>', 'IP列表', list)

program
.command('deploy <name>')
.description('部署一个服务节点')
.action(function(name){
console.log('Deploying "%s"', name);
});

program.parse(process.argv);

console.log(' chdir - %s ', program.chdir);
console.log(' config - %s ', program.config);
console.log(' max: %j', program.max);
console.log(' seed: %j', program.seed);
program.range = program.range || [];
console.log(' range: %j..%j', program.range[0], program.range[1]);
console.log(' list: %j', program.list);
```

### 查看帮助信息

```{bash}
~ D:\workspace\javascript\nodejs-commander>node myServer.js -h

Usage: myServer.js test

Commands:

deploy <name> 部署一个服务节点

Options:

-h, --help output usage information
-V, --version output the version number
-C, --chdir [value] 设置服务器节点
-c, --config [value] 设置配置文件
-m, --max <n> 最大连接数
-s, --seed <n> 出始种子
-r, --range <a>..<b> 阈值区间
-l, --list <items> IP列表
```

### 通过命令行启动：

```{bash}
~ D:\workspace\javascript\nodejs-commander>node myServer.js -c /deploy/c1/config.conf -m 20 -s 19.1 -r 12..101 -l 192.168.1.1,192.168.1.2,192.168.1.3 deploy server1

Deploying "server1"
 chdir - /home/conan/server
 config - /deploy/c1/config.conf
 max: 20
 seed: 19.1
 range: 12..101
 list: ["192.168.1.1","192.168.1.2","192.168.1.3"]
```

我们按照需求，设计了一个自定义的服务器的启动命令。

## 5. 发布为运行命令

### 其实就是简单地，把myServer.js改写一下。定义启动脚本：

```{bash}
#!/usr/bin/env node
```

### 新建命令文件bin/myServer

```{bash}
~ D:\workspace\javascript\nodejs-commander>mkdir bin
~ vi myServer

#!/usr/bin/env node

var program = require('commander');

function range(val) {
    return val.split('..').map(Number);
}

function list(val) {
    return val.split(',');
}

program
    .version('0.0.1')
    .usage('test')
    .option('-C, --chdir [value]', '设置服务器节点','/home/conan/server')
    .option('-c, --config [value]', '设置配置文件','./deploy.conf')
    .option('-m, --max ', '最大连接数', parseInt)
    .option('-s, --seed ', '出始种子', parseFloat)
    .option('-r,--range <a>..<b>', '阈值区间', range)
    .option('-l, --list ', 'IP列表', list)

program
    .command('deploy ')
    .description('部署一个服务节点')
    .action(function(name){
        console.log('Deploying "%s"', name);
    });

program.parse(process.argv);

console.log(' chdir - %s ', program.chdir);
console.log(' config - %s ', program.config);
console.log(' max: %j', program.max);
console.log(' seed: %j', program.seed);
program.range = program.range || [];
console.log(' range: %j..%j', program.range[0], program.range[1]);
console.log(' list: %j', program.list);
```

### 以命令方式运行

```{bash}
~ D:\workspace\javascript\nodejs-commander>node bin\myServer -c /deploy/c1/config.conf -m 20 -s 19.1 -r 12..101 -l 192.168
.1.1,192.168.1.2,192.168.1.3 deploy server1

Deploying "server1"
 chdir - /home/conan/server
 config - /deploy/c1/config.conf
 max: 20
 seed: 19.1
 range: 12..101
 list: ["192.168.1.1","192.168.1.2","192.168.1.3"]
```

#### 转载请注明出处：http://blog.fens.me/nodejs-commander/

