Nodejs异步流程控制Async
========

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-async/

![Nodejs异步流程控制Async](http://blog.fens.me/wp-content/uploads/2013/09/nodejs-async.png)

#### 前言

Nodejs框架类库很多，功能相近的框架，本来只打算学一种写一种。之前写过流程控制框架[windjs文章](http://blog.fens.me/nodejs-async-windjs/)，本来是想着要支持一下“国人框架”。无奈啊，作者竟然放弃了维护，国人真的不靠谱啊！

“流程控制”本来是件比较简单的事，但是由于Nodejs的异步架构的实现方法，对于需要同步的业务逻辑，实现起来就比较麻烦。嵌套3-4层，代码就会变得的支离破碎了！

今天就遇到了一个业务逻辑，连续对数据库操作，前后有依赖。让我们看看Async是如何解决问题的。

不用不知道，一用真强大！！

#### 目录

1. Async介绍
2. Async安装
3. Async函数介绍
4. async_demo使用介绍
5. 场景：对数据库的连续操作
6. async_demo我的分支

## 1. Async介绍

Async是一个流程控制工具包，提供了直接而强大的异步功能。基于Javascript为Node.js设计，同时也可以直接在浏览器中使用。

Async提供了大约20个函数，包括常用的 map, reduce, filter, forEach 等，异步流程控制模式包括，串行(series)，并行(parallel)，瀑布(waterfall)等。

项目地址：https://github.com/caolan/async

## 2. Async安装

### 我的系统环境

* win7 64bit
* Nodejs:v0.10.5
* Npm:1.2.19
* MySQL:Server version: 5.6.11 MySQL Community Server (GPL)

### 我们做实验时，安装async有两个方式:

1. 独立安装async
2. 下载async demo代码安装

我建议大家用第二种方式安装，这样子实例的代码就都有了。

### 1). 独立安装async

```{bash}

~ D:\workspace\javascript>mkdir nodejs-async && cd nodejs-async
~ D:\workspace\javascript\nodejs-async>npm install async
npm http GET https://registry.npmjs.org/async
npm http 304 https://registry.npmjs.org/async
async@0.2.9 node_modules\async
```

打开网页,参照示例学习：https://github.com/bsspirit/async_demo

### 2). 下载async demo代码安装

```{bash}
~ D:\workspace\javascript>git clone git@github.com:bsspirit/async_demo.git nodejs-async
~ D:\workspace\javascript>cd nodejs-async
~ D:\workspace\javascript\nodejs-async>npm install
npm http GET https://registry.npmjs.org/moment
npm http GET https://registry.npmjs.org/async
npm http 304 https://registry.npmjs.org/moment
npm http 304 https://registry.npmjs.org/async
async@0.2.9 node_modules\async
moment@2.1.0 node_modules\moment
```

这套demo示例，比较全面的介绍了async的使用，有中文注释。 感谢github社区原创作者freewind，代码更新的贡献者alsotang。

当然，我的分支中也修改了一部分代码。在本文最后，我会写到changelog中！

## 3. Async函数介绍

基于async的0.2.9版本。

### async主要实现了三个部分的流程控制功能：

* 集合: Collections
* 流程控制: Control Flow
* 工具类: Utils

#### 1). 集合: Collections

* each: 如果想对同一个集合中的所有元素都执行同一个异步操作。
* map: 对集合中的每一个元素，执行某个异步操作，得到结果。所有的结果将汇总到最终的callback里。与each的区别是，each只关心操作不管最后的值，而map关心的最后产生的值。
* filter: 使用异步操作对集合中的元素进行筛选, 需要注意的是，iterator的callback只有一个参数，只能接收true或false。
* reject: reject跟filter正好相反，当测试为true时则抛弃
* reduce: 可以让我们给定一个初始值，用它与集合中的每一个元素做运算，最后得到一个值。reduce从左向右来遍历元素，如果想从右向左，可使用reduceRight。
* detect: 用于取得集合中满足条件的第一个元素。
* sortBy: 对集合内的元素进行排序，依据每个元素进行某异步操作后产生的值，从小到大排序。
* some: 当集合中是否有至少一个元素满足条件时，最终callback得到的值为true，否则为false.
* every: 如果集合里每一个元素都满足条件，则传给最终回调的result为true，否则为false
* concat: 将多个异步操作的结果合并为一个数组。

#### 2). 流程控制: Control Flow

* series: 串行执行，一个函数数组中的每个函数，每一个函数执行完成之后才能执行下一个函数。
* parallel: 并行执行多个函数，每个函数都是立即执行，不需要等待其它函数先执行。传给最终callback的数组中的数据按照tasks中声明的顺序，而不是执行完成的顺序。
* whilst: 相当于while，但其中的异步调用将在完成后才会进行下一次循环。
* doWhilst: 相当于do…while, doWhilst交换了fn,test的参数位置，先执行一次循环，再做test判断。
* until: until与whilst正好相反，当test为false时循环，与true时跳出。其它特性一致。
* doUntil: doUntil与doWhilst正好相反，当test为false时循环，与true时跳出。其它特性一致。
* forever: 无论条件循环执行，如果不出错，callback永远不被执行。
* waterfall: 按顺序依次执行一组函数。每个函数产生的值，都将传给下一个。
* compose: 创建一个包括一组异步函数的函数集合，每个函数会消费上一次函数的返回值。把f(),g(),h()异步函数，组合成f(g(h()))的形式，通过callback得到返回值。
* applyEach: 实现给一数组中每个函数传相同参数，通过callback返回。如果只传第一个参数，将返回一个函数对象，我可以传参调用。
* queue: 是一个串行的消息队列，通过限制了worker数量，不再一次性全部执行。当worker数量不够用时，新加入的任务将会排队等候，直到有新的worker可用。
* cargo: 一个串行的消息队列，类似于queue，通过限制了worker数量，不再一次性全部执行。不同之处在于，cargo每次会加载满额的任务做为任务单元，只有任务单元中全部执行完成后，才会加载新的任务单元。
* auto: 用来处理有依赖关系的多个任务的执行。
* iterator: 将一组函数包装成为一个iterator，初次调用此iterator时，会执行定义中的第一个函数并返回第二个函数以供调用。
* apply: 可以让我们给一个函数预绑定多个参数并生成一个可直接调用的新函数，简化代码。
* nextTick: 与nodejs的nextTick一样，再最后调用函数。
* times: 异步运行,times可以指定调用几次，并把结果合并到数组中返回
* timesSeries: 与time类似，唯一不同的是同步执行

#### 3). 工具类: Utils

* memoize: 让某一个函数在内存中缓存它的计算结果。对于相同的参数，只计算一次，下次就直接拿到之前算好的结果。
* unmemoize: 让已经被缓存的函数，返回不缓存的函数引用。
* log: 执行某异步函数，并记录它的返回值，日志输出。
* dir: 与log类似，不同之处在于，会调用浏览器的console.dir()函数，显示为DOM视图。
* noConflict: 如果之前已经在全局域中定义了async变量，当导入本async.js时，会先把之前的async变量保存起来，然后覆盖它。仅仅用于浏览器端，在nodejs中没用，这里无法演示。

## 4. async_demo使用介绍

详细使用请参考github源代码：https://github.com/bsspirit/async_demo

每个函数的用法，有非常详细的实例！！

## 5. 场景：对数据库的连续操作

这个场景进背景情况，请参考文章：用Nodejs连接MySQL

原场景中，对数据串行操作，增删改查(CRUD)，代码如下：

```{bash}

var mysql = require('mysql');
var conn = mysql.createConnection({
    host: 'localhost',
    user: 'nodejs',
    password: 'nodejs',
    database: 'nodejs',
    port: 3306
});
conn.connect();

var insertSQL = 'insert into t_user(name) values("conan"),("fens.me")';
var selectSQL = 'select * from t_user limit 10';
var deleteSQL = 'delete from t_user';
var updateSQL = 'update t_user set name="conan update"  where name="conan"';

//delete
conn.query(deleteSQL, function (err0, res0) {
    if (err0) console.log(err0);
    console.log("DELETE Return ==> ");
    console.log(res0);

    //insert
    conn.query(insertSQL, function (err1, res1) {
        if (err1) console.log(err1);
        console.log("INSERT Return ==> ");
        console.log(res1);

        //query
        conn.query(selectSQL, function (err2, rows) {
            if (err2) console.log(err2);

            console.log("SELECT ==> ");
            for (var i in rows) {
                console.log(rows[i]);
            }

            //update
            conn.query(updateSQL, function (err3, res3) {
                if (err3) console.log(err3);
                console.log("UPDATE Return ==> ");
                console.log(res3);

                //query
                conn.query(selectSQL, function (err4, rows2) {
                    if (err4) console.log(err4);

                    console.log("SELECT ==> ");
                    for (var i in rows2) {
                        console.log(rows2[i]);
                    }
                });
            });
        });
    });
});

//conn.end();
```

为了实现了串行操作，所有的调用都是在callback中实现的，5层嵌套结构。这种代码已经变得不可以维护了。所以，需要用async库，对上面的代码结构进行重写！

### 修改后的代码

```{bash}
var mysql = require('mysql');
var async = require('async');

var conn = mysql.createConnection({
    host: 'localhost',
    user: 'nodejs',
    password: 'nodejs',
    database: 'nodejs',
    port: 3306
});

var sqls = {
    'insertSQL': 'insert into t_user(name) values("conan"),("fens.me")',
    'selectSQL': 'select * from t_user limit 10',
    'deleteSQL': 'delete from t_user',
    'updateSQL': 'update t_user set name="conan update"  where name="conan"'
};

var tasks = ['deleteSQL', 'insertSQL', 'selectSQL', 'updateSQL', 'selectSQL'];
async.eachSeries(tasks, function (item, callback) {
    console.log(item + " ==> " + sqls[item]);
    conn.query(sqls[item], function (err, res) {
        console.log(res);
        callback(err, res);
    });
}, function (err) {
    console.log("err: " + err);
});
```

### 控制台输出

```{bash}
deleteSQL ==> delete from t_user
{ fieldCount: 0,
  affectedRows: 0,
  insertId: 0,
  serverStatus: 34,
  warningCount: 0,
  message: '',
  protocol41: true,
  changedRows: 0 }
insertSQL ==> insert into t_user(name) values("conan"),("fens.me")
{ fieldCount: 0,
  affectedRows: 2,
  insertId: 45,
  serverStatus: 2,
  warningCount: 0,
  message: '&Records: 2  Duplicates: 0  Warnings: 0',
  protocol41: true,
  changedRows: 0 }
selectSQL ==> select * from t_user limit 10
[ { id: 45,
    name: 'conan',
    create_date: Fri Sep 13 2013 12:24:51 GMT+0800 (中国标准时间) },
  { id: 46,
    name: 'fens.me',
    create_date: Fri Sep 13 2013 12:24:51 GMT+0800 (中国标准时间) } ]
updateSQL ==> update t_user set name="conan update"  where name="conan"
{ fieldCount: 0,
  affectedRows: 1,
  insertId: 0,
  serverStatus: 2,
  warningCount: 0,
  message: '(Rows matched: 1  Changed: 1  Warnings: 0',
  protocol41: true,
  changedRows: 1 }
selectSQL ==> select * from t_user limit 10
[ { id: 45,
    name: 'conan update',
    create_date: Fri Sep 13 2013 12:24:51 GMT+0800 (中国标准时间) },
  { id: 46,
    name: 'fens.me',
    create_date: Fri Sep 13 2013 12:24:51 GMT+0800 (中国标准时间) } ]
err: null
```

代码一下读性就增强了许多倍，这就是高效的开发。

不用不知道，一用真强大！！！

当然还有其他的工作流框架来完成这件事情step,then.js,windjs。
windjs请参考：wind.js助力异步编程

## 6. async_demo我的分支

https://github.com/bsspirit/async_demo

1. forEach.js改名为each.js
2. each.js文件中的async.forEach，改为async.each
3. map.js增加mapLimit函数
4. filter_reject.js对注释补充
5. filter_reject.js增加rejectSeries函数
6. reduce.js增加注释
7. detect.js增加注释
8. sortBy.js增加注释
9. some.js对注释补充
10. every.js对注释补充和修改
11. series.js调整注释格式
12. parallel.js调整注释格式
13. parallel.js增加parallelLimit函数
14. whilist_until.js调整注释格式
15. whilist_until.js增加doWhilst函数
16. whilist_until.js增加doUntil函数
17. whilist_until.js增加forever函数
18. waterfall.js调整注释格式
19. 增加compose.js文件
20. apply.js补充注释并增加一个实例
21. 修改nextTick.js实例
22. 增加times.js文件，包括times和timesSeries函数
23. 修改iterator.js实例
24. 修正auto.js关于第二个实例的错误解释
25. 修改queue.js实例和注释
26. 修改cargo.js文件
27. 增加applyEach.js文件
28. 修改utils.js实例和注释

#### 转载请注明出处：http://blog.fens.me/nodejs-async/

