让Nodejs来管理定时任务later
============

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-cron-later/‎

![让Nodejs来管理定时任务later](http://blog.fens.me/wp-content/uploads/2013/12/nodejs-later.png)

#### 前言

一个完整的系统少不了定时任务，大多数情况我们都选用使用Linux CRON，通过操作系统命令进行定时任务。当我们要维护多台计算机，几十个，几百个定时任务的时候，用CRON会带来非常大的运维成本。可能写到程序中，就是一个不错的选择了。

Later提供了一个Nodejs的定时任务解决方案，让我来看看他是怎么工作的吧！！

#### 目录

1. 什么是定时任务？
2. Later介绍
3. Later安装
4. Later基本使用
5. Later Schedules – 设置时间表
6. Later Time Periods – 时间定义和时间计算
7. Later Modifiers – 行为修饰符
8. Later Parsers – 规则解释器
9. Later Occurrences – 时间控制
10. Later Executing – 启动运行

## 1. 什么是定时任务？

定时任务(计划任务)，是任务在约定的时间执行已经计划好的工作，这是表面的意思。在Linux中，我们经常用到 cron 服务器来完成这项工作。cron服务器可以根据配置文件约定的时间来执行特定的作务。比如我们可以在配置文件中约定每天早上4点，对httpd 服务器重新启动，这就是一个计划任务；

crontab文件的格式：M H D m d cmd.

* M: 分钟（0-59）。
* H：小时（0-23）。
* D：天（1-31）。
* m: 月（1-12）。
* d: 一星期内的天（0~6，0为星期天）。

每两个小时

```{bash}
0 */2 * * * echo "Have a break now." >> /tmp/test.txt
```

晚上11点到早上8点之间每两个小时，早上八点

```{bash}
0 23-7/2，8 * * * echo "Have a good dream：）" >> /tmp/test.txt
```

每个月的4号和每个礼拜的礼拜一到礼拜三的早上11点

```{bash}
0 11 4 * 1-3 command line
```

1月1日早上4点

```{bash}
0 4 1 1 * command line
```

>关于CRON的介绍，摘自：http://baike.baidu.com/view/660015.htm

## 2. Later介绍

Later是一个基于Nodejs的工具库，用最简单的方式执行定时任务。Later可以运行在Node和浏览器中。

Later.js官方主页：http://bunkat.github.io/later/index.html

## 3. Later安装

Later可以运行在Node和浏览器中，分别用npm和bower进行依赖管理。关于NPM的介绍，请参考：[快速创建基于npm的nodejs库](http://blog.fens.me/nodejs-npm-package/), 关于Bower的介绍，请参考：[bower解决js的依赖管理](http://blog.fens.me/nodejs-bower-intro/)

### 系统环境

* Linux: Ubuntu 12.04.2 LTS 64bit deskop
* Nodejs: npm 1.2.21
* node v0.11.2

### 安装later

```{bash}

~ cd /home/conan/nodejs
~ mkdir nodejs-later && cd nodejs-later

~ sudo npm install later
npm http GET https://registry.npmjs.org/later
npm http 200 https://registry.npmjs.org/later
npm http GET https://registry.npmjs.org/later/-/later-1.1.6.tgz
npm http 200 https://registry.npmjs.org/later/-/later-1.1.6.tgz
later@1.1.6 node_modules/later
```

没有其他包的依赖，这样就安装完成了！

## 4. Later基本使用

创建一个每5分钟启动的定时器规则，输出启动时间。

创建启动文件：app.js

```{bash}

~ vi app.js

var later = require('later');
var sched = later.parse.text('every 5 mins'),
    occurrences = later.schedule(sched).next(10);

for(var i=0;i<10;i++){
    console.log(occurrences[i]);
}
```

运程程序：

```{bash}
~ node app.js
Thu Dec 26 2013 10:45:00 GMT+0800 (CST)
Thu Dec 26 2013 10:50:00 GMT+0800 (CST)
Thu Dec 26 2013 10:55:00 GMT+0800 (CST)
Thu Dec 26 2013 11:00:00 GMT+0800 (CST)
Thu Dec 26 2013 11:05:00 GMT+0800 (CST)
Thu Dec 26 2013 11:10:00 GMT+0800 (CST)
Thu Dec 26 2013 11:15:00 GMT+0800 (CST)
Thu Dec 26 2013 11:20:00 GMT+0800 (CST)
Thu Dec 26 2013 11:25:00 GMT+0800 (CST)
Thu Dec 26 2013 11:30:00 GMT+0800 (CST)
```

## 5. Later Schedules - 设置时间表

Schedules模块用来设置定时规则，提供3种规则设置。

* Basic schedules：基本时间表
* Composite schedules: 组合时间表
* Exception schedules: 异常时间表

### 1). Basic schedules：基本时间表

设置每日10:15am , 10:45am启动

```{bash}
var basic = {h: [10], m: [15,45]};
```

### 2). Composite schedules: 组合时间表

设置每日10:15am , 10:45am, 和17:40pm 启动

```{bash}
 var composite = [
    {h: [10], m: [15,45]},
    {h: [17], m: [30]}
  ];
```

### 3). Exception schedules: 异常时间表

用于设置一下无效的日期：设置 每年1月 和 每周一，六，日 时间表无效

```{bash}
var exception = [
    {M: [1]},
    {dw: [1,6,7]}
];
```

### 4). 程序实现

新建测试文件：schedules.js

```{bash}

~ vi schedules.js

var later = require('later');

var basic = {h: [10], m: [15,45]};
var composite = [
    basic,
    {h: [17], m: [30]}
];
var exception = [
    {M: [1]},
    {dw: [1,6,7]}
];

var sched = {
    schedules:composite,
    exceptions:exception
};

later.date.localTime();

console.log("Now:"+new Date());
var occurrences = later.schedule(sched).next(10);
for(var i = 0; i < occurrences.length; i++) {
    console.log(occurrences[i]);
}
```

运行程序

```{bash}
~ node schedules.js
Now:Thu Dec 26 2013 11:40:27 GMT+0800 (CST)
Thu Dec 26 2013 17:30:00 GMT+0800 (CST)
Mon Dec 30 2013 10:15:00 GMT+0800 (CST)
Mon Dec 30 2013 10:45:00 GMT+0800 (CST)
Mon Dec 30 2013 17:30:00 GMT+0800 (CST)
Tue Dec 31 2013 10:15:00 GMT+0800 (CST)
Tue Dec 31 2013 10:45:00 GMT+0800 (CST)
Tue Dec 31 2013 17:30:00 GMT+0800 (CST)
Mon Feb 03 2014 10:15:00 GMT+0800 (CST)
Mon Feb 03 2014 10:45:00 GMT+0800 (CST)
Mon Feb 03 2014 17:30:00 GMT+0800 (CST)
```

当前时间为：2013-12-26 11:40:27

从结果中看到，接下来的10个时间点。

* 第1个：2013-12-26 17:30:00
* 第2个：2013-12-30 10:15:00 (排除了2013-12-27,28,29的3天)
* 第8个：2014-02-01 10:15:00 (排除了2014的1月份)

还有比这种方式，更便捷定义时间表的么！！太神奇了！

## 6. Later Time Periods - 时间定义和时间计算

Time Periods模块用于时间定义和时间计算。

### 1). 时间定义

在之前的代码中，我们是这样的定义的

```{bash}
{h: [17], m: [30]}
```

h代表小时，m代表分钟。

时间定义完整列表：

* Second, s: 秒, 取值范围:[0-59]
* minute, m：分, 取值范围:[0-59]
* hour, h: 时, 取值范围:[0-23]
* time, t: 秒每日, 取值范围:[0-86399]
* day, D: 日, 取值范围:[1-31]
* dayOfWeek, dw, d: 日每周, 取值范围:[1-7]
* dayOfYear, dy: 日每年，取值范围:[1-365]
* weekOfMonth, wm: 周每月，取值范围:[1-5]
* weekOfYear, wy: 周每年，取值范围:[1-52]
* month, M: 月，取值范围:[1-12]
* year, Y: 年，取值范围:[1970-2099]

### 2). 时间计算

* name: 名称
* range: 取值范围计数
* val(date): 当前时间段的值
* isValid(date, value): 检验输入是否是当前时间段的值
* extent(date): 取值范围
* start(date): 开始时间点
* end(date): 结束时间点
* next(date, value): value之后的时间点
* prev(date, value): value之前的时间点

### 3). 程序实现

新建测试文件：time.js

```{bash}
~ vi time.js

var later = require('later');
later.date.localTime();

var d = new Date();

console.log(d);
console.log(later.hour.name);
console.log(later.hour.range);
console.log(later.hour.val(d));
console.log(later.hour.isValid(d, 2));
console.log(later.hour.isValid(d, 12));
console.log(later.hour.extent());
console.log(later.hour.start(d));
console.log(later.hour.end(d));
console.log(later.hour.next(d, 5));
console.log(later.hour.prev(d, 21));
```

运行程序

```{bash}
~ node time.js

Thu Dec 26 2013 12:30:42 GMT+0800 (CST)
hour
3600
12
false
true
[ 0, 23 ]
Thu Dec 26 2013 12:00:00 GMT+0800 (CST)
Thu Dec 26 2013 12:59:59 GMT+0800 (CST)
Fri Dec 27 2013 05:00:00 GMT+0800 (CST)
Wed Dec 25 2013 21:59:59 GMT+0800 (CST)
```

输出结果的解释：

* 当前时间：2013-12-26 12:30:42
* 以小时定义时间
* 每小时3600个计数点
* 当前时间段的的值是12
* 检查2不是当前时间段的值
* 检查12不是当前时间段的值
* 取值范围[0，23]
* 当前时间段的开始时间点：12:00:00
* 当前时间段的结束时间点：12:59:59
* 下一个周期第5个时间段开始点：2013-12-27 05:00:00
* 上一个周期第21个时间段结束点：2013-12-25 21:59:59

## 7. Later Modifiers - 行为修饰符

Later可以编写自定义修饰符，可以改变现有的时间周期的行为，通过 _(modifier-id) 这样的格式定义。

* after(_a): 之后时间修饰符
* before(_b): 之前时间修饰符

### 1). after(_a)

以小时定义，设置17:00pm之前都是合法的时间

```{bash}
var demo1_a = {schedules: [{h_a: [17]}]};

#等价定义
var demo1 = {schedules: [{h: [17,18,19,20,21,22,23]}]};
```

### 2). before(_b)

以小时定义，设置17:00pm之后都是合法的时间

```{bash}
var demo2_b = {schedules: [{h_b: [17]}]};

#等价定义
var demo2 = {schedules: [{h: [0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16]}]};
```

### 3). 程序实现

新建文件：modifiers.js

```{bash}
~ vi modifiers.js

var later = require('later');
later.date.localTime();

console.log("Now:"+new Date());

var demo1_a = {schedules: [{h_a: [17]}]};
var demo2_b = {schedules: [{h_b: [17]}]};

var occurrences = later.schedule(demo1_a).next(3);
for(var i = 0; i < occurrences.length; i++) {
    console.log(occurrences[i]);
}

occurrences = later.schedule(demo2_b).next(3);
for(var i = 0; i < occurrences.length; i++) {
    console.log(occurrences[i]);
}
```

运行程序

```{bash}
~ node modifiers.js

Now:Thu Dec 26 2013 13:04:06 GMT+0800 (CST)
Thu Dec 26 2013 17:00:00 GMT+0800 (CST)
Thu Dec 26 2013 18:00:00 GMT+0800 (CST)
Thu Dec 26 2013 19:00:00 GMT+0800 (CST)
Thu Dec 26 2013 13:04:06 GMT+0800 (CST)
Thu Dec 26 2013 14:00:00 GMT+0800 (CST)
Thu Dec 26 2013 15:00:00 GMT+0800 (CST)
```

当前时间：2013-12-26 13:04:06

* 17点后的时间表(h_a)：17:00:00
* 17点前的时间表(h_b)：13:04:06

Later支持我们可以定义自己的修饰符，有兴趣的同学自己查文档吧。

## 8. Later Parsers - 规则解释器

Parsers模块提供了3种规则解释器，方便定义时间表。

* Recur: 链式API定义
* Cron Parser: CRON格式定义
* Text Parser：自然语言定义

## 1). Recur: 链式API定义

设置每小时第5分0秒启动

```{bash}
var sched = later.parse.recur().on(5).minute();
```

时间定义API

```{bash}
  second();
  minute();
  hour();
  time();
  dayOfWeek();
  dayOfWeekCount();
  dayOfMonth();
  dayOfYear();
  weekOfMonth();
  weekOfYear();
  month();
  year();
```

时间计算API

* on(vals): 设置时间值
* first(): 最小的时间值
* last(): 最大的时间值
* onWeekend(): 周末，等价于on(1,7).dayOfWeek()
* onWeekday(): 工作日，等价于on(2,3,4,5,6).dayOfWeek()
* every(val): 循环每个时间
* after(val): 在之后
* before(val): 在之前
* startingOn(val): 每个时间段开始的偏移量
* between(start, end): 在两个时间之间
* and():
* except():
* customPeriod(id):
* customModifier(id, vals):

### 2). Cron Parser: CRON格式定义

通过原CRON格式进行定义。

设置每小时第5分0秒启动

```{bash}
var cron = later.parse.cron('5 * * * *');
```

### 3). Text Parser：自然语言定义

通过关键字格式进行定义。

```{bash}
var text = later.parse.text('every 5th mins');
```

## 9. Later Occurrences - 时间控制

时区设置

```{bash}
//默认UTF时区
later.date.UTC();

//设置本地时区
later.date.localTime();
```

构造对象

```{bash}
var schedule = {schedules: [{m: [5]}]};
var occurrences = later.schedule(schedule);
```

时间控制API

* later.schedule(schedule).next(count, start, end): 取下N个有效时间点
* later.schedule(schedule).prev(count, start, end): 取上N个有效时间点
* later.schedule(schedule).nextRange(count, start, end): 取下N个有效时间段
* later.schedule(schedule).prevRange(count, start, end): 取上N个有效时间段

## 10. Later Executing - 启动运行

Executing模块定义了setTimeout和setInterval两种方式，实现运行。

* setTimeout: 设置一段时间后运行，只运行1次
* setInterval: 循环运行，直到clear

### 1). setTimeout

定义：5秒后运行，只运行一次！

新建文件：settimeout.js

```{bash}

~ vi settimeout.js

var later = require('later');
later.date.localTime();

console.log("Now:"+new Date());

var sched = later.parse.recur().every(5).second(),
    t = later.setTimeout(function() {
        test(5);
    }, sched);

function test(val) {
   console.log(new Date());
   console.log(val);
}
```

运行程序

```{bash}
~ node settimeout.js
Now:Thu Dec 26 2013 14:12:36 GMT+0800 (CST)
Thu Dec 26 2013 14:12:40 GMT+0800 (CST)
5
```

### 2). setInterval

定义：2秒后运行，循环运行，直到15秒后，clear停止！

新建文件：setinterval.js

```{bash}
~ vi setInterval.js

var later = require('later');
later.date.localTime();

console.log("Now:"+new Date());

var sched = later.parse.recur().every(2).second(),
    t = later.setInterval(function() {
        test(Math.random(10));
    }, sched);

function test(val) {
   console.log(new Date());
   console.log(val);
}

setTimeout(function(){
   t.clear();
   console.log("Clear");
},15*1000);
```

运行程序

```{bash}
~  node setinterval.js
Now:Thu Dec 26 2013 14:17:54 GMT+0800 (CST)
Thu Dec 26 2013 14:17:56 GMT+0800 (CST)
0.5084630874916911
Thu Dec 26 2013 14:17:58 GMT+0800 (CST)
0.47506075259298086
Thu Dec 26 2013 14:18:00 GMT+0800 (CST)
0.957129133399576
Thu Dec 26 2013 14:18:02 GMT+0800 (CST)
0.7480122991837561
Thu Dec 26 2013 14:18:04 GMT+0800 (CST)
0.9212428922764957
Thu Dec 26 2013 14:18:06 GMT+0800 (CST)
0.030472515616565943
Thu Dec 26 2013 14:18:08 GMT+0800 (CST)
0.9528024469036609
Clear
```

#### 转载请注明出处：http://blog.fens.me/nodejs-cron-later/‎

