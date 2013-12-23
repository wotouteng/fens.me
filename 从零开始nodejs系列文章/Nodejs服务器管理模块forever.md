Nodejs服务器管理模块forever
============

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-server-forever/

![](http://blog.fens.me/wp-content/uploads/2013/09/nodejs-forever.png)

#### 前言

又说到服务器管理了，上次说的时候用的是[Linux系统服务upstart](http://blog.fens.me/linux-upstart-nodejs/), 今天准备尝试一下Nodejs自己模块化解决方案forever。

服务器管理是系统上线后，必须要面对的问题。最好有一个软件可以提供整套的服务器运行解决方案：要求运行稳定，支持高并发，启动/停止命令简单，支持热部署，宕机重启，监控界面和日志，集群环境。

接下来，就让我们看看forever能不能实现目标。

#### 目录

1. forever介绍
2. forever安装
3. forever命令行的中文解释
4. forever服务器管理
5. forever在Ubuntu进行服务器管理
6. 模拟服务器宕机
7. 开发环境和生产环境的启动配置

## 1. forever介绍

forever是一个简单的命令式nodejs的守护进程，能够启动，停止，重启App应用。forever完全基于命令行操作，在forever进程之下，创建node的子进程，通过monitor监控node子进程的运行情况，一旦文件更新，或者进程挂掉，forever会自动重启node服务器，确保应用正常运行。

## 2. forever安装

全局安装forever

```{bash}
~ D:\workspace\javascript>npm install -g forever
D:\toolkit\nodejs\forever -> D:\toolkit\nodejs\node_modules\forever\bin\forever
D:\toolkit\nodejs\foreverd -> D:\toolkit\nodejs\node_modules\forever\bin\foreverd
```

查看forever帮助

```{bash}

~ D:\workspace\javascript>forever -h
help: usage: forever [action] [options] SCRIPT [script-options]
help:
help: Monitors the script specified in the current process or as a daemon
help:
help: actions:
help: start Start SCRIPT as a daemon
help: stop Stop the daemon SCRIPT
help: stopall Stop all running forever scripts
help: restart Restart the daemon SCRIPT
help: restartall Restart all running forever scripts
help: list List all running forever scripts
help: config Lists all forever user configuration
help: set <key> <val> Sets the specified forever config <key>
help: clear <key> Clears the specified forever config <key>
help: logs Lists log files for all forever processes
help: logs <script|index> Tails the logs for <script|index>
help: columns add <col> Adds the specified column to the output in `forever list`
help: columns rm <col> Removed the specified column from the output in `forever list`
help: columns set <cols> Set all columns for the output in `forever list`
help: cleanlogs [CAREFUL] Deletes all historical forever log files
help:
help: options:
help: -m MAX Only run the specified script MAX times
help: -l LOGFILE Logs the forever output to LOGFILE
help: -o OUTFILE Logs stdout from child script to OUTFILE
help: -e ERRFILE Logs stderr from child script to ERRFILE
help: -p PATH Base path for all forever related files (pid files, etc.)
help: -c COMMAND COMMAND to execute (defaults to node)
help: -a, --append Append logs
help: -f, --fifo Stream logs to stdout
help: -n, --number Number of log lines to print
help: --pidFile The pid file
help: --sourceDir The source directory for which SCRIPT is relative to
help: --minUptime Minimum uptime (millis) for a script to not be considered "spinning"
help: --spinSleepTime Time to wait (millis) between launches of a spinning script.
help: --colors --no-colors will disable output coloring
help: --plain alias of --no-colors
help: -d, --debug Forces forever to log debug output
help: -v, --verbose Turns on the verbose messages from Forever
help: -s, --silent Run the child script silencing stdout and stderr
help: -w, --watch Watch for file changes
help: --watchDirectory Top-level directory to watch from
help: --watchIgnore To ignore pattern when watch is enabled (multiple option is allowed)
help: -h, --help You're staring at it
help:
help: [Long Running Process]
help: The forever process will continue to run outputting log messages to the console.
help: ex. forever -o out.log -e err.log my-script.js
help:
help: [Daemon]
help: The forever process will run as a daemon which will make the target process start
help: in the background. This is extremely useful for remote starting simple node.js scripts
help: without using nohup. It is recommended to run start with -o -l, & -e.
help: ex. forever start -l forever.log -o out.log -e err.log my-daemon.js
help: forever stop my-daemon.js
help:
```

通过“[Commander写自己的Nodejs命令](http://blog.fens.me/nodejs-commander/)”一文的介绍，我们已经知道如何看懂，并实现自定义的命令行了。虽然，forever不是基于Commander的，而是基于另一个命令行开发包optimist，原理都是类似的。

所以，我们看到forever支持的命令和配置项确实不少，应该是偏命令行的管理工具。

## 3. forever命令行的中文解释

### 子命令actions：

+ start:启动守护进程
+ stop:停止守护进程
+ stopall:停止所有的forever进程
+ restart:重启守护进程
+ restartall:重启所有的foever进程
+ list:列表显示forever进程
+ config:列出所有的用户配置项
+ set <key> <val>: 设置用户配置项
+ clear <key>: 清楚用户配置项
+ logs: 列出所有forever进程的日志
+ logs <script|index>: 显示最新的日志
+ columns add <col>: 自定义指标到forever list
+ columns rm <col>: 删除forever list的指标
+ columns set<cols>: 设置所有的指标到forever list
+ cleanlogs: 删除所有的forever历史日志

### 配置参数options：

+ -m MAX: 运行指定脚本的次数
+ -l LOGFILE: 输出日志到LOGFILE
+ -o OUTFILE: 输出控制台信息到OUTFILE
+ -e ERRFILE: 输出控制台错误在ERRFILE
+ -p PATH: 根目录
+ -c COMMAND: 执行命令，默认是node
+ -a, –append: 合并日志
+ -f, –fifo: 流式日志输出
+ -n, –number: 日志打印行数
+ –pidFile: pid文件
+ –sourceDir: 源代码目录
+ –minUptime: 最小spinn更新时间(ms)
+ –spinSleepTime: 两次spin间隔时间
+ –colors: 控制台输出着色
+ –plain: –no-colors的别名，控制台输出无色
+ -d, –debug: debug模式
+ -v, –verbose: 打印详细输出
+ -s, –silent: 不打印日志和错误信息
+ -w, –watch: 监控文件改变
+ –watchDirectory: 监控顶级目录
+ –watchIgnore: 通过模式匹配忽略监控
+ -h, –help: 命令行帮助信息

## 4. forever服务器管理

创建一个web项目(express3+ejs)，使用forever管理服务器。

安装express3

```{bash}
~ D:\workspace\javascript>express -e nodejs-forever
~ D:\workspace\javascript>cd nodejs-forever && npm install
```

通过forever启动应用

```{bash}
~ D:\workspace\javascript\nodejs-forever>forever start app.js
warn:    --minUptime not set. Defaulting to: 1000ms
warn:    --spinSleepTime not set. Your script will exit if it does not stay up for at least 1000ms
info:    Forever processing file: app.js
```

打开浏览器: http://localhost:3000，可以看到web界面

在win下面查看forever状态

```{bash}

~ D:\workspace\javascript\nodejs-forever>forever list
info:    No forever processes running

~ D:\workspace\javascript\nodejs-forever>forever stop app.js
error:   Forever cannot find process with index: app.js
```

我们发现forever的程序，工作不对了！！程序明明是运行状态，通过list确看不到。接下来，切换成Linux Ubuntu继续测试。

## 5. forever在Ubuntu进行服务器管理

### Linux的系统环境

* Linux: Ubuntu 12.04.2 64bit Server
* Node: v0.11.2
* Npm: 1.2.21

初始化项目：安装命令不解释了

```{bash}

~ cd /home/conan/nodejs
~ express -e nodejs-forever
~ cd nodejs-forever && npm install
~ sudo npm install forever -g
```

启动forever

```{bash}
~ forever start app.js
warn:    --minUptime not set. Defaulting to: 1000ms
warn:    --spinSleepTime not set. Your script will exit if it does not stay up for at least 1000ms
info:    Forever processing file: app.js
```

查看node服务器状态

```{bash}

~ forever list
info:    Forever processes running
data:        uid  command             script forever pid   logfile                       uptime
data:    [0] L2tY /usr/local/bin/node app.js 18276   18279 /home/conan/.forever/L2tY.log 0:0:0:37.792

# 系统进程
~ ps -aux|grep node
Warning: bad ps syntax, perhaps a bogus '-'? See http://procps.sf.net/faq.html
conan    18296  0.5  1.1 597696 23776 ?        Ssl  15:48   0:00 /usr/local/bin/node /usr/local/lib/node_modules/forever/bin/monitor app.js
conan    18299  0.4  0.8 630340 18392 ?        Sl   15:48   0:00 /usr/local/bin/node /home/conan/nodejs/nodejs-forever/app.js

# 端口占用
~ netstat -nltp|grep node
tcp        0      0 0.0.0.0:3000            0.0.0.0:*               LISTEN      18299/node
```

停止服务器

```{bash}
~ forever stop app.js
info:    Forever stopped process:
data:        uid  command             script forever pid   logfile                       uptime
[0] L2tY /usr/local/bin/node app.js 18276   18279 /home/conan/.forever/L2tY.log 0:0:0:45.621
```

我们看到在Linux Ubuntu环境中是正常的。

## 6. 模拟服务器宕机

两种测试方案：

1. 用Linux命令，直接杀死node进程
2. 在应用中，模拟异常退出

### 1). 用Linux命令，直接杀死node进程

```{bash}
# 查看node进程，PID=18299  
~ ps -aux|grep node

conan    18296  0.0  1.1 597696 23776 ?        Ssl  15:48   0:00 /usr/local/bin/node /usr/local/lib/node_modules/forever/bin/monitor app.js
conan    18299  0.0  0.8 630340 18392 ?        Sl   15:48   0:00 /usr/local/bin/node /home/conan/nodejs/nodejs-forever/app.js
conan    18315  0.0  0.0  13584   956 pts/5    R+   15:52   0:00 grep --color=auto node

# 杀死PID=19299
~ kill -9 18299

# 再看node进程，node自动重启，新的PID=18324  
~ ps -aux|grep node

conan    18296  0.0  1.1 597696 23916 ?        Ssl  15:48   0:00 /usr/local/bin/node /usr/local/lib/node_modules/forever/bin/monitor app.js
conan    18316  2.6  0.8 630340 18412 ?        Sl   15:52   0:00 /usr/local/bin/node /home/conan/nodejs/nodejs-forever/app.js
conan    18324  0.0  0.0  13584   956 pts/5    R+   15:52   0:00 grep --color=auto node
```

我们看到看杀死node进程，forever会帮助我们，重启node。

杀死forever的monitor

```{bash}

~ kill -9  18296
~ ps -aux|grep node

conan    18316  0.0  0.9 630340 18644 ?        Sl   15:52   0:00 /usr/local/bin/node /home/conan/nodejs/nodejs-forever/app.js
conan    18333  0.0  0.0  13584   952 pts/5    S+   15:57   0:00 grep --color=auto node

# 再杀死node进程
~ kill -9 18316
~ ps -aux|grep node

conan    18336  0.0  0.0  13584   956 pts/5    S+   15:58   0:00 grep --color=auto node
```

我们尝试杀死了forever的monitor，monitor程序没有自动重启，然后再杀死node进程后，node也不会自动重启了。

### 2). 在应用中，模拟异常退出

修改文件：app.js

```{bash}

~ vi app.js

//..
http.createServer(app).listen(app.get('port'), function(){
  console.log(new Date());
  console.log('Express server listening on port ' + app.get('port'));
});

setTimeout(function(){
  console.log(new Date());
  throw new Error('App is error from inner!');
},10*1000);
```

通过node命令启动

```{bash}
~ node app.js
Thu Sep 26 2013 16:08:44 GMT+0800 (CST)
Express server listening on port 3000
Thu Sep 26 2013 16:08:54 GMT+0800 (CST)

/home/conan/nodejs/nodejs-forever/app.js:41
  throw new Error('App is error from inner!');
        ^
Error: App is error from inner!
    at null._onTimeout (/home/conan/nodejs/nodejs-forever/app.js:41:9)
    at Timer.listOnTimeout [as ontimeout] (timers.js:110:15)
```

10秒后，由于内部错误， node进程挂掉了。

通过forever命令启动

```{bash}

~ mkdir logs
~ chmod 777 -R logs
~ forever -p . -l ./logs/access.log -e ./logs/error.log start app.js

# 检查错误日志
~ cat logs/access.log ls
Thu Sep 26 2013 16:15:02 GMT+0800 (CST)
Express server listening on port 3000
Thu Sep 26 2013 16:15:12 GMT+0800 (CST)

/home/conan/nodejs/nodejs-forever/app.js:41
  throw new Error('App is error from inner!');
        ^
Error: App is error from inner!
    at null._onTimeout (/home/conan/nodejs/nodejs-forever/app.js:41:9)
    at Timer.listOnTimeout [as ontimeout] (timers.js:110:15)
error: Forever detected script exited with code: 8
error: Forever restarting script for 1 time
Thu Sep 26 2013 16:15:13 GMT+0800 (CST)
Express server listening on port 3000
Thu Sep 26 2013 16:15:23 GMT+0800 (CST)

/home/conan/nodejs/nodejs-forever/app.js:41
  throw new Error('App is error from inner!');
        ^
Error: App is error from inner!
    at null._onTimeout (/home/conan/nodejs/nodejs-forever/app.js:41:9)
    at Timer.listOnTimeout [as ontimeout] (timers.js:110:15)
error: Forever detected script exited with code: 8
error: Forever restarting script for 2 time
Thu Sep 26 2013 16:15:23 GMT+0800 (CST)
Express server listening on port 3000
Thu Sep 26 2013 16:15:33 GMT+0800 (CST)

/home/conan/nodejs/nodejs-forever/app.js:41
  throw new Error('App is error from inner!');
        ^
Error: App is error from inner!
    at null._onTimeout (/home/conan/nodejs/nodejs-forever/app.js:41:9)
    at Timer.listOnTimeout [as ontimeout] (timers.js:110:15)
error: Forever detected script exited with code: 8
error: Forever restarting script for 3 time
Thu Sep 26 2013 16:15:33 GMT+0800 (CST)
Express server listening on port 3000
Thu Sep 26 2013 16:15:43 GMT+0800 (CST)

/home/conan/nodejs/nodejs-forever/app.js:41
  throw new Error('App is error from inner!');
        ^
Error: App is error from inner!
    at null._onTimeout (/home/conan/nodejs/nodejs-forever/app.js:41:9)
    at Timer.listOnTimeout [as ontimeout] (timers.js:110:15)
error: Forever detected script exited with code: 8
error: Forever restarting script for 4 time
```

我们发现每10秒种，node内部挂掉，然后再被forever重启！！

通过list我们手动刷新几次也可以看到pid是变的。

```{bash}
~ forever list
info:    Forever processes running
data:        uid  command             script forever pid   logfile         uptime
data:    [0] SmtT /usr/local/bin/node app.js 18444   18579 logs/access.log 0:0:0:7.211

~ forever list
info:    Forever processes running
data:        uid  command             script forever pid   logfile         uptime
data:    [0] SmtT /usr/local/bin/node app.js 18444   18579 logs/access.log 0:0:0:8.921

~ forever list
info:    Forever processes running
data:        uid  command             script forever pid   logfile         uptime
data:    [0] SmtT /usr/local/bin/node app.js 18444   18604 logs/access.log 0:0:0:0.177

~ forever list
info:    Forever processes running
data:        uid  command             script forever pid   logfile         uptime
data:    [0] SmtT /usr/local/bin/node app.js 18444   18604 logs/access.log 0:0:0:2.206
```

这样forever就帮助我们完成了，几项比较重要服务器管理功能：“启动/停止命令简单”，“支持热部署”，“宕机重启”，“监控界面和日志”。

比起[upstart](http://blog.fens.me/linux-upstart-nodejs/)管理，省略了配置脚本的步骤(/etc/init/nodejs-xx.conf)。其他的功能，还要更近一步的使用才知道。

## 7. 开发环境和生产环境的启动配置

### 开发环境

```{bash}
~ cd /home/conan/nodejs/nodejs-forever/
~ forever -p . -l ./logs/access.log -e ./logs/error.log -a -w start app.js
```

### 生产环境

```{bash}
~ export LOG=/var/log/nodejs/project
~ export PID=/var/log/nodejs/project/forever.pid
~ export APP_PATH=/home/conan/nodejs/nodejs-forever
~ export APP=$APP_PATH/app.js

~ forever -p $APP_PATH -l $LOG/access.log -e $LOG/error.log -o $LOG/out.log -a --pidFile $PID start $APP
```

#### 转载请注明出处：http://blog.fens.me/nodejs-server-forever/

