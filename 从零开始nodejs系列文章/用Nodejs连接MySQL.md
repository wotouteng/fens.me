用Nodejs连接MySQL
===========

#### 从零开始nodejs系列文章

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-enviroment/

![用Nodejs连接MySQL](http://blog.fens.me/wp-content/uploads/2013/09/nodejs-mysql.png)

#### 前言

MySQL是一款常用的开源数据库产品，通常也是免费数据库的首选。查了一下NPM列表，发现Nodejs有13库可以访问MySQL，felixge/node-mysql似乎是最受关注项目，我也决定尝试用一下。

要注意名字，”felixge/node-mysql”非”node-mysql”，安装部分会介绍这个小插曲！

#### 目录

1. node-mysql介绍
2. 建立MySQL测试库
3. node-mysql安装
4. node-mysql使用

## 1. node-mysql介绍

felixge/node-mysql是一个纯nodejs的用javascript实现的一个MySQL客户端程序。felixge/node-mysql封装了Nodejs对MySQL的基本操作，100% MIT公共许可证。

项目地址：https://github.com/felixge/node-mysql

## 2. 建立MySQL测试库

本地创建MySQL测试库：nodejs

```{bash}
~ mysql -uroot -p
mysql> CREATE DATABASE nodejs;
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| nodejs             |
| performance_schema |
+--------------------+
4 rows in set (0.00 sec)

mysql> GRANT ALL ON nodejs.* to nodejs@'%' IDENTIFIED BY 'nodejs';
mysql> GRANT ALL ON nodejs.* to nodejs@localhost IDENTIFIED BY 'nodejs';
```

重新登陆MySQL

```{bash}
C:\Users\Administrator>mysql -unodejs -p
Enter password: ******

mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| nodejs             |
| test               |
+--------------------+
3 rows in set (0.00 sec)

mysql> USE nodejs
Database changed
```

新建一个user表

```{bash}
CREATE TABLE t_user(
id INT PRIMARY KEY AUTO_INCREMENT,
name VARCHAR(16) NOT NULL ,
create_date TIMESTAMP NULL DEFAULT now()
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
CREATE UNIQUE INDEX t_quiz_IDX_0 on t_user(name);

mysql> SHOW TABLES;
+------------------+
| Tables_in_nodejs |
+------------------+
| t_user           |
+------------------+
1 row in set (0.04 sec)
```

## 3. node-mysql安装

### 我的系统环境

+ win7 64bit
+ Nodejs:v0.10.5
+ Npm:1.2.19
+ MySQL:Server version: 5.6.11 MySQL Community Server (GPL)

### 创建工程:nodejs-node-mysql

```{bash}
~ D:\workspace\javascript>mkdir nodejs-node-mysql
~ D:\workspace\javascript>cd nodejs-node-mysql
~ D:\workspace\javascript\nodejs-node-mysql>npm install node-mysql
node-mysql@0.2.0 node_modules\node-mysql
├── better-js-class@0.1.2
├── cps@0.1.7
├── underscore@1.5.2
└── mysql@2.0.0-alpha9 (require-all@0.0.3, bignumber.js@1.0.1)
```

### 这里有一个小插曲

安装“node-mysql”后，打开package.json文件发现，这个项目地址是

```{bash}
https://github.com/redblaze/node-mysql.git
```

从依赖关系可以看到，它依赖于mysql库，是对felixge/node-mysql的封装。

![](http://blog.fens.me/wp-content/uploads/2013/09/node-mysql1.png)

由于这个项目star是0，fork也是0. 所以，我也不准备花时间测试了，重新安装felixge/node-mysql的包。

### 重新安装node-mysql

```{bash}
~ D:\workspace\javascript\nodejs-node-mysql>rm -rf node_modules
~ D:\workspace\javascript\nodejs-node-mysql>npm install mysql@2.0.0-alpha9
npm http GET https://registry.npmjs.org/mysql/2.0.0-alpha9
npm http 200 https://registry.npmjs.org/mysql/2.0.0-alpha9
npm http GET https://registry.npmjs.org/mysql/-/mysql-2.0.0-alpha9.tgz
npm http 200 https://registry.npmjs.org/mysql/-/mysql-2.0.0-alpha9.tgz
npm http GET https://registry.npmjs.org/require-all/0.0.3
npm http GET https://registry.npmjs.org/bignumber.js/1.0.1
npm http 304 https://registry.npmjs.org/require-all/0.0.3
npm http 304 https://registry.npmjs.org/bignumber.js/1.0.1
mysql@2.0.0-alpha9 node_modules\mysql
├── require-all@0.0.3
└── bignumber.js@1.0.1
```

这回就对了，继续下面的开发！

创建node程序启动文件：app.js

### 第一个测试

```{bash}
~ vi app.js

var mysql = require('mysql');
var conn = mysql.createConnection({
    host: 'localhost',
    user: 'nodejs',
    password: 'nodejs',
    database:'nodejs',
    port: 3306
});
conn.connect();
conn.query('SELECT 1 + 1 AS solution', function(err, rows, fields) {
    if (err) throw err;
    console.log('The solution is: ', rows[0].solution);
});
conn.end();
```

运行node

```{bash}
~ D:\workspace\javascript\nodejs-node-mysql>node app.js
The solution is:  2
```

这样我们就让Nodejs连接上了MySQL。

## 4. node-mysql使用

下面我们要对node-mysql的API进行常用的测试。

+ 表新删改查
+ 连接池配置
+ MySQL断线重连
+ 连接池超时测试

### 1). 表新删改查

修改app.js

```{bash}

~ vi app.js

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

控制台输出：

```{bash}
D:\workspace\javascript\nodejs-node-mysql>node app.js
DELETE Return ==>
{ fieldCount: 0,
  affectedRows: 2,
  insertId: 0,
  serverStatus: 34,
  warningCount: 0,
  message: '',
  protocol41: true,
  changedRows: 0 }
INSERT Return ==>
{ fieldCount: 0,
  affectedRows: 2,
  insertId: 33,
  serverStatus: 2,
  warningCount: 0,
  message: '&Records: 2  Duplicates: 0  Warnings: 0',
  protocol41: true,
  changedRows: 0 }
SELECT ==>
{ id: 33,
  name: 'conan',
  create_date: Wed Sep 11 2013 12:09:15 GMT+0800 (中国标准时间) }
{ id: 34,
  name: 'fens.me',
  create_date: Wed Sep 11 2013 12:09:15 GMT+0800 (中国标准时间) }
UPDATE Return ==>
{ fieldCount: 0,
  affectedRows: 1,
  insertId: 0,
  serverStatus: 2,
  warningCount: 0,
  message: '(Rows matched: 1  Changed: 1  Warnings: 0',
  protocol41: true,
  changedRows: 1 }
SELECT ==>
{ id: 33,
  name: 'conan update',
  create_date: Wed Sep 11 2013 12:09:15 GMT+0800 (中国标准时间) }
{ id: 34,
  name: 'fens.me',
  create_date: Wed Sep 11 2013 12:09:15 GMT+0800 (中国标准时间) }
```

由于node的异步的，上面是一个连续的操作，代码会被写的支离破碎。我们可以通过async库对上面代码进行封装，请参考文章：[Nodejs异步流程控制Async](http://blog.fens.me/nodejs-async/)

### 2). 连接池配置

增加文件：app-pooling.js

```{bash}
~ vi app-pooling.js

var mysql = require('mysql');
var pool = mysql.createPool({
    host: 'localhost',
    user: 'nodejs',
    password: 'nodejs',
    database: 'nodejs',
    port: 3306
});

var selectSQL = 'select * from t_user limit 10';

pool.getConnection(function (err, conn) {
    if (err) console.log("POOL ==> " + err);

    conn.query(selectSQL,function(err,rows){
        if (err) console.log(err);
        console.log("SELECT ==> ");
        for (var i in rows) {
            console.log(rows[i]);
        }
        conn.release();
    });
});
```

控制台输出：

```{bash}
D:\workspace\javascript\nodejs-node-mysql>node app-pooling.js
SELECT ==>
{ id: 39,
  name: 'conan update',
  create_date: Wed Sep 11 2013 13:41:18 GMT+0800 (中国标准时间) }
{ id: 40,
  name: 'fens.me',
  create_date: Wed Sep 11 2013 13:41:18 GMT+0800 (中国标准时间) }
```

### 3). MySQL断线重连

分别模拟3种错误

+ 登陆密码错误
+ 数据库宕机
+ 数据库连接超时

#### 新增文件：app-reconnect.js

```{bash}
~ vi app-reconnect.js

var mysql = require('mysql');
var conn;
function handleError () {
    conn = mysql.createConnection({
        host: 'localhost',
        user: 'nodejs',
        password: 'nodejs',
        database: 'nodejs',
        port: 3306
    });

    //连接错误，2秒重试
    conn.connect(function (err) {
        if (err) {
            console.log('error when connecting to db:', err);
            setTimeout(handleError , 2000);
        }
    });

    conn.on('error', function (err) {
        console.log('db error', err);
        // 如果是连接断开，自动重新连接
        if (err.code === 'PROTOCOL_CONNECTION_LOST') {
            handleError();
        } else {
            throw err;
        }
    });
}
handleError();
```

#### a. 模拟密码错误

修改password: ‘nodejs11′

控制台输出。

```{bash}
D:\workspace\javascript\nodejs-node-mysql>node app-reconnect.js
error when connecting to db: { [Error: ER_ACCESS_DENIED_ERROR: Access denied for user 'nodejs'@'localhost' (using pass
rd: YES)]
  code: 'ER_ACCESS_DENIED_ERROR',
  errno: 1045,
  sqlState: '28000',
  fatal: true }
error when connecting to db: { [Error: ER_ACCESS_DENIED_ERROR: Access denied for user 'nodejs'@'localhost' (using pass
rd: YES)]
  code: 'ER_ACCESS_DENIED_ERROR',
  errno: 1045,
  sqlState: '28000',
  fatal: true }
```

#### b. 模拟数据库宕机

正常启动node，然后杀掉mysqld的进程。

控制台输出。

```{bash}

D:\workspace\javascript\nodejs-node-mysql>node app-reconnect.js
db error { [Error: read ECONNRESET]
  code: 'ECONNRESET',
  errno: 'ECONNRESET',
  syscall: 'read',
  fatal: true }

Error: read ECONNRESET
    at errnoException (net.js:884:11)
    at TCP.onread (net.js:539:19)
```

这个异常，直接导致node程序被杀死！

#### c. 模拟连接超时，PROTOCOL_CONNECTION_LOST

切换到root账户, 修改MySQL的wait_timeout参数，设置为10毫秒超时。

```{bash}
~ mysql -uroot -p
mysql> show variables like 'wait_timeout';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| wait_timeout  | 28800 |
+---------------+-------+
1 row in set (0.00 sec)

mysql> set global wait_timeout=10;
Query OK, 0 rows affected (0.00 sec)

mysql> show variables like 'wait_timeout';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| wait_timeout  | 10    |
+---------------+-------+
1 row in set (0.00 sec)
```

修改文件：app-reconnection.js，在最后增加代码

```{bash}
~ vi app-reconnection.js

function query(){
    console.log(new Date());
    var sql = "show variables like 'wait_timeout'";
    conn.query(sql, function (err, res) {
        console.log(res);
    });
}

query();
setInterval(query, 15*1000);
```

程序会每融15秒，做一次查询。

控制台输出

```{bash}
D:\workspace\javascript\nodejs-node-mysql>node app-reconnect.js
Wed Sep 11 2013 15:21:14 GMT+0800 (中国标准时间)
[ { Variable_name: 'wait_timeout', Value: '10' } ]
db error { [Error: Connection lost: The server closed the connection.] fatal: true, code: 'PROTOCOL_CONNECTION_LOST' }
Wed Sep 11 2013 15:21:28 GMT+0800 (中国标准时间)
[ { Variable_name: 'wait_timeout', Value: '10' } ]
db error { [Error: Connection lost: The server closed the connection.] fatal: true, code: 'PROTOCOL_CONNECTION_LOST' }
Wed Sep 11 2013 15:21:43 GMT+0800 (中国标准时间)
[ { Variable_name: 'wait_timeout', Value: '10' } ]
```

我们自己的程序捕获了“PROTOCOL_CONNECTION_LOST”异常，并自动的实现了数据库重连。

### 4). MySQL连接池的超时测试

针对wait_timeout问题，我们再对连接做一下测试。

修改app-pooling.js文件

```{bash}

var mysql = require('mysql');
var pool = mysql.createPool({
    host: 'localhost',
    user: 'nodejs',
    password: 'nodejs',
    database: 'nodejs',
    port: 3306
});

var selectSQL ="show variables like 'wait_timeout'";

pool.getConnection(function (err, conn) {
    if (err) console.log("POOL ==> " + err);

    function query(){
        conn.query(selectSQL, function (err, res) {
            console.log(new Date());
            console.log(res);
            conn.release();
        });
    }
    query();
    setInterval(query, 5000);
});
```

控制台输出：

```{bash}
D:\workspace\javascript\nodejs-node-mysql>node app-pooling.js
Wed Sep 11 2013 15:32:25 GMT+0800 (中国标准时间)
[ { Variable_name: 'wait_timeout', Value: '10' } ]
Wed Sep 11 2013 15:32:30 GMT+0800 (中国标准时间)
[ { Variable_name: 'wait_timeout', Value: '10' } ]
Wed Sep 11 2013 15:32:35 GMT+0800 (中国标准时间)
[ { Variable_name: 'wait_timeout', Value: '10' } ]
```

连接池，已经解决了自动重连的问题了，后面我们的开发，可以尽量使用pooling的方式。

#### 转载请注明出处：http://blog.fens.me/nodejs-mysql-intro/




























