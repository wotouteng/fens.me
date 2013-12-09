Commander写自己的Nodejs命令
====================

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com
 

#### 转载请注明出处
http://blog.fens.me/nodejs-commander/


![Commander写自己的Nodejs命令](http://blog.fens.me/wp-content/uploads/2013/09/commander.png)

## 前言

在使用Nodejs过程中，有很多包都支持全局安装，然后提供一个命令，然后在命令行我们就可以完成一些任务，像 express, grunt, bower, yeoman, reap, karma, requirejs 等。有时候，我们也需要自己开发这样的命令行工具。

commander.js，可以帮助我们简化命令行的开发。

## 目录

1. commander介绍
2. commander安装
3. commander的API
4. 开发自定义的命令
5. 发布为运行命令
 
## 1. commander介绍

commander是一个轻巧的nodejs模块，提供了用户命令行输入和参数解析强大功能。commander源自一个同名的Ruby项目。

commander的特性：

* 自记录代码
* 自动生成帮助
* 合并短参数（“ABC”==“-A-B-C”）
* 默认选项
* 强制选项​​
* 命令解析
* 提示符

## 2. commander安装

我的系统环境

* win7 64bit
* Nodejs:v0.10.5
* Npm:1.2.19

安装commander

```{shell}
~ D:\workspace\javascript>mkdir nodejs-commander && cd nodejs-commander
~ D:\workspace\javascript\nodejs-commander>npm install commander
npm http GET https://registry.npmjs.org/commander
npm http 304 https://registry.npmjs.org/commander
commander@2.0.0 node_modules\commander
```

编写一个简单的例子：增加文件app.js

```{javascript}
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


