AngularJS最理想开发工具WebStorm
============

[AngularJS体验式编程系列文章](http://blog.fens.me/series-angular/)，将介绍如何用angularjs构建一个强大的web前端系统。angularjs是由Google团队开发的一款非常优秀web前端框架。在当前如此多的web框架下，angularjs能脱颖而出，从架构设计上就高人一等，双向数据绑定，依赖注入，指令，MVC，模板。Angular.js创新地把后台技术融入前端开发，扫去jQuery一度的光芒。用angularjs就像写后台代码，更规范，更结构化，更可控。

#### 关于作者

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/angularjs-webstorm-ide/

![AngularJS最理想开发工具WebStorm](http://blog.fens.me/wp-content/uploads/2013/08/webstorm.png)

#### 前言

俗语讲：“工欲善其事，必先利其器”，为了玩转AngularJS，顺手的工具要选好。在上一篇文章中，通过[yeoman我们构建了一个AngularJS的工程](http://blog.fens.me/angularjs-yeoman-project/)，通过vi编辑器和命令行操作，我们几乎可以完成整个的项目开发。但是，随着代码量和文件数的增加，文件和目录越来越难把握，代码文件各种难懂，缩进不一致，controller和directive被多少个地方所引用，。。。

我们需要一个IDE，来帮助我们更好的看懂项目，管理代码。WebStorm是目前我认为AngularJS开发最理想工具。

#### 目录

1. WebStorm介绍
2. 导入Javascript工程
3. 安装AngularJS插件
4. WebStorm使用

## 1. WebStorm介绍

WebStorm 是jetbrains公司旗下一款JavaScript 开发工具。被广大中国JS开发者誉为“Web前端开发神器”、“最强大的HTML5编辑器”、“最智能的JavaSscript IDE”等。与IntelliJ IDEA同源，继承了IntelliJ IDEA强大的JS部分的功能。

下载：http://www.jetbrains.com/webstorm/index.html

WebStorm可能最大的缺点就是需要付费，对于个人开发者49刀。不过，WebStorm还推出了几种免费的liences。

* Classroom License:申请课堂演示
* Open Source Project License:申请做为开源项目的贡献者
* MVP License: 申请微软的专家

可以通过购买页面找到信息：http://www.jetbrains.com/webstorm/buy/index.jsp

当然我是不会告诉大家，在google里可以找到过去版本的liences的，只是中国的程序员真是太苦逼了。

## 2. 导入Javascript工程

打开WebStorm后，我们可以通过操作：

```{bash}
"file"==>"open Directory..."==>"select Directory" ==> "ok"
```

来选择我们已经构建好的工程。

![](http://blog.fens.me/wp-content/uploads/2013/08/webstorm1.png)

WebStrom对Html5, javascript, css，json… 大部分的代码都支持非常好。

![](http://blog.fens.me/wp-content/uploads/2013/08/webstorm2.png)

## 3. 安装AngularJS插件

不过，默认的WebStrom并不认识，AngularJS的指令，当我尝试写代码ng-controller时，编辑器没有出现对应该的提示。

```{bash}
<div ng
```

![](http://blog.fens.me/wp-content/uploads/2013/08/webstorm3.png)

对于特别熟悉的AngularJS的人来说，并不是那么重要。但是，如果有语法提示不是会更好吗？

安装AngularJS插件，两种办法：

1. 在IDE中，自动下载自动安装
2. 手动下载，手动安装

### 1). 在IDE中，自动下载自动安装

我们需要以下的操作：

1. File->Settings->Plugins
2. 点击 “Browse Repositories”
3. 选中 “AngularJS” 双击
4. 选择 “Yes”
5. 重启WebStorm

![](http://blog.fens.me/wp-content/uploads/2013/08/webstorm4.png)

我在使用这个方法的时候失败了

### 2). 手动下载，手动安装
我们在jetbrains的插入库中，找到AngularJS：http://plugins.jetbrains.com/plugin/6971

下载angularjs-plugin.zip，然后解压到D:\toolkit\WebStorm 6.0.1\plugins\目录

~ D:\toolkit\WebStorm 6.0.1\plugins\angularjs-plugin
重启WebStorm

### 再次输入代码：

```{bash}
<div ng-c
```

语法的提示，太帅气了！！

我查了一下插件的开发者的代码，支持全部的angularJS指令语法：

```{bash}
ng-app,ng-bind,ng-bind-html-unsafe,ng-bind-template,ng-class,ng-class-even,ng-class-odd,
ng-cloak,ng-controller,ng-form,ng-hide,ng-include,ng-init,ng-non-bindable,ng-pluralize,
ng-repeat,ng-show,ng-submit,ng-style,ng-switch,ng-switch-when,ng-switch-default,ng-options,
ng-view,ng-transclude,ng-model,ng-list,ng-change,ng-value,ng-required,required
```

## 4. WebStorm使用

### 1). 对于HTML的页面：WebStorm会在右上角出现浏览器的图标方便我们打开测试

![](http://blog.fens.me/wp-content/uploads/2013/08/webstorm6.png)

### 2). 对Javascript脚本：WebStorm会方便的配置NodeJS启动, UnitTest启动 等等

![](http://blog.fens.me/wp-content/uploads/2013/08/webstorm7.png)

IDE的功能很强大，熟悉后会极大地提升我们的开发效率的，特别是对于大规模的多人项目。

#### 转载请注明出处：http://blog.fens.me/angularjs-webstorm-ide/




