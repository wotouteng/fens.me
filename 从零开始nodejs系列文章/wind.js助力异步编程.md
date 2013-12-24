wind.js助力异步编程
==========

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-async-windjs/

![wind.js助力异步编程](http://blog.fens.me/wp-content/uploads/2013/08/windjs.png)

#### 前言

Nodejs是一个单线程，异步，事件驱动的Javascript平台架构，异步编程正是Nodejs的基础。有时候我们需要做些同步的事情，程序写起来就会变得有点麻烦。通常的解决办法有2种，一种是设置setTimeout时间，这样做会让我们的程序挂住，无法执行并发请求，另一种是callback嵌套，这样做性能上没有问题，但是代码看上去会非常零散，增大维护的难度。

Wind.js是由国人开发的一款异步流程控制类库，提供了一种“顺序编写、异步执行”的操作。一旦你用上了，就一定不会放手的。

#### 目录

1. Wind.js介绍
2. Wind.js安装
3. 场景一：独立事件，并行
4. 场景二：依赖事件，串行
5. 场景三：组合事件，并行+串行

## 1. Wind.js介绍

Wind.js是很有特点的一个JavaScript异步编程类库（其前身为Jscex）, Wind.js的唯一目的便是“改善编程体验”，改善的“程度”以及改善的“方式”便是Wind.js与其他异步流程控制方案最大的区别。Wind.js的设计思路便是将这种抽象构建为JavaScript本身。通过$await函数标识对代码进行自动重写，JIT编译(Just in Time)，从而避免人工的预编译。

我们可以在github上面找到这个项目：https://github.com/JeffreyZhao/wind

## 2. Wind.js安装

### 我的系统环境

* win7: 64bit
* npm: 1.2.19
* node: v0.10.5

### Wind.js安装

```{bash}

~ D:\workspace\javascript>mkdir node-windjs
~ D:\workspace\javascript>cd node-windjs

D:\workspace\javascript\node-windjs>npm install wind
npm http GET https://registry.npmjs.org/wind
npm http 200 https://registry.npmjs.org/wind
npm http GET https://registry.npmjs.org/wind/-/wind-0.7.3.tgz
npm http 200 https://registry.npmjs.org/wind/-/wind-0.7.3.tgz
npm WARN package.json wind@0.7.3 No readme data!
wind@0.7.3 node_modules\wind
```

## 3. 场景一：独立事件，并行

接下来我们要做的事情，会非常有意思。

### 定义3个函数A(),B(),C()

* A():耗时3秒完成
* B():耗时5秒完成
* C():瞬时时完

A(),B(),C()为3个独立事件，并行运行

![](http://blog.fens.me/wp-content/uploads/2013/08/stage1.png)

```{bash}

var A = function(){
    setTimeout(function(){
        console.log("Finish A");
    },3000);
    console.log("Start A");
}

var B = function(){
    setTimeout(function(){
        console.log("Finish B");
    },5000);
    console.log("Start B");
}

var C = function(){
    console.log("Start C");
    console.log("Finish C");
}

A();
B();
C();

//结果 
Start A
Start B
Start C
Finish C
Finish A
Finish B
```

程序执行没有任何问题，按照执行时间，打印出了Finish的顺序：C,A,B

直接使用Javascript代码，可以很好地完成功能。

## 4. 场景二：依赖事件，串行

A(),B(),C()串行

![](http://blog.fens.me/wp-content/uploads/2013/08/stage2.png)

### 1). callback嵌套方式

```{bash}
var A = function(){
    setTimeout(function(){
        console.log("Finish A");
        B();
    },3000);
    console.log("Start A");
}

var B = function(){
    setTimeout(function(){
        console.log("Finish B");
        C();
    },5000);
    console.log("Start B");
}

var C = function(){
    console.log("Start C");
    console.log("Finish C");
}

A();

// 结果
Start A
Finish A
Start B
Finish B
Start C
Finish C
```

虽然，我们实现A,B,C串行的结果，但是代码看起来就有些不好看了。B()的调入被嵌入到了A()函数中，C()的调用也被嵌入到的B()的函数中。如果我们的调用流程再长些，回调嵌套就会成为挥之不去的噩梦啊！！

### 2). Wind.js方式

```{bash}

var Wind = require("wind");
var Task =  Wind.Async.Task;

var A = eval(Wind.compile("async", function () {
    console.log("Start A");
    $await(Wind.Async.sleep(3000));
    console.log("Finish A");
}));

var B = eval(Wind.compile("async", function () {
    console.log("Start B");
    $await(Wind.Async.sleep(5000));
    console.log("Finish B");
}));

var C = eval(Wind.compile("async", function () {
    console.log("Start C");
    console.log("Finish C");
}));

var task = eval(Wind.compile("async", function () {
    $await(A());
    $await(B());
    $await(C());
}));
task().start();

//结果 
Start A
Finish A
Start B
Finish B
Start C
Finish C
```

结果就是我们想要的，我们是按照$await(A()),$await(B()),$await(C())的顺序定义的，而且顺序执行的。比起callback嵌套方式的，大有改观，更便于以后的代码维护。

## 5. 场景三：组合事件，并行+串行

先A(),B()并行，都完成后再运行C()

![](http://blog.fens.me/wp-content/uploads/2013/08/stage3.png)

### 1). callback嵌套方式

同时，引入一个全局变量stat，判断A,B的完成情况。

```{bash}
var A = function(){
    setTimeout(function(){
        console.log("Finish A");
        if(++stat == 2) C();
    },3000);
    console.log("Start A");
}

var B = function(){
    setTimeout(function(){
        console.log("Finish B");
        if(++stat == 2) C();
    },5000);
    console.log("Start B");
}

var C = function(){
    console.log("Start C");
    console.log("Finish C");
}

var stat=0;
A();
B();

//结果
Start A
Start B
Finish A
Finish B
Start C
Finish C
```

虽然实现功能，但代码已经混乱了。我相信大部分人都不愿意去维护这样的代码的。

### 2). Wind.js方式

```{bash}
var Wind = require("wind");
var Task =  Wind.Async.Task;

var A = eval(Wind.compile("async", function () {
    console.log("Start A");
    $await(Wind.Async.sleep(3000));
    console.log("Finish A");
}));

var B = eval(Wind.compile("async", function () {
    console.log("Start B");
    $await(Wind.Async.sleep(5000));
    console.log("Finish B");
}));

var C = eval(Wind.compile("async", function () {
    console.log("Start C");
    console.log("Finish C");
}));

var task = eval(Wind.compile("async", function () {
    $await(Task.whenAll(A(),B()));
    $await(C());
}));
task().start();

//结果
Start A
Start B
Finish A
Finish B
Start C
Finish C
```

wind.js又一次拯救我们的代码，这样的代码有章有法，看起来才舒服。

wind.js帮助我们很好地规范了异步编程，用过了，你还舍得放弃吗？

#### 转载请注明出处：http://blog.fens.me/nodejs-async-windjs/

