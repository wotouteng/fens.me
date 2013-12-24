Nodejs临时文件回收器reap
============

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-gc-reap/

![Nodejs临时文件回收器reap](http://blog.fens.me/wp-content/uploads/2013/09/reap.png)

#### 前言
生产环境中，临时目录总是容易被我们忽略考虑的问题。当通过服务器上传文件时，一般文件流先会产生在临时目录中，然后再通过重命名，复制等后续操作保存在到存储目录。我们总是会想，临时目录(/tmp)的文件，操作系统当空间不够的时候，会自动回收的，所以就放之不管。

可是操作系统的回收，并不是像人脑那么智能。也许就是在服务器IO最繁忙的时候，他发出了要回收临时目录的命令。服务器卡几秒，就真不好说了。特别是对于时时可靠性要求很高的系统，几秒都是不可忍受的。

让人脑来解决临时目录清理的问题吧！用reap只需几行命令。

#### 目录

1. reap介绍
2. reap的命令使用
3. reap程序开发

## 1. reap介绍

reap是一个简单小巧的临时文件回收器。reap有一个监控的调度，可以设置间隔时间，删除指定目录中的文件，并把日志打印到控制台。

一般情况服务都是在Linux环境，win也可以运行！

## 2. reap的命令使用

### 我的系统环境

* Linux: Ubuntu 12.04.2 64bit Server
* Node: v0.11.2
* Npm: 1.2.21

全局安装reap

```{bash}

~ mkdir nodejs-reap && cd nodejs-reap
~ sudo npm install -g reap
/usr/local/bin/reap -> /usr/local/lib/node_modules/reap/bin/reap
npm WARN package.json cookie-signature@1.0.1 No repository field.
npm WARN package.json methods@0.0.1 No repository field.
npm WARN package.json range-parser@0.0.4 No repository field.
npm WARN package.json dateformat@1.0.2-1.2.3 No repository field.
npm WARN package.json bytes@0.2.0 No repository field.
npm WARN package.json pause@0.0.1 No repository field.
npm WARN package.json uid2@0.0.2 No repository field.
npm WARN package.json policyfile@0.0.4 'repositories' (plural) Not supported.
npm WARN package.json Please pick one as the 'repository' field
npm WARN package.json assert-plus@0.1.2 No repository field.
npm WARN package.json ctype@0.5.2 No repository field.
npm WARN package.json cheerio-select@0.0.3 No repository field.
npm WARN package.json github-url-from-git@1.1.1 No repository field.
reap@0.1.0 /usr/local/lib/node_modules/reap
├── ms@0.6.1
├── bytes@0.2.1
├── batch@0.4.0
├── debug@0.7.2
└── commander@1.2.0 (keypress@0.1.0)
```

reap的命令

```{bash}
~ reap -h
  
  Usage: reap [options] 
  Options:
    -h, --help            output usage information
    -t, --threshold   file unlink threshold [1 hour]
    -s, --single          run a single time and exit
```

* -h, –help: 打印帮助信息
* -t, –threshold: 后台运行，设置间隔时间(ms)
* -s, –single: 单次运行

我们新建两个目录，用来测试。

* tmp目录:临时目录，被reap监控并清空
* files目录:源文件目录，放置一些文件

```{bash}

~ mkdir tmp
~ mkdir files

~ ls -l files/
-rw-r--r-- 1 conan conan  303  8月  2 09:50 github.Rproj
-rw-rw-r-- 1 conan conan  821  7月 31 20:19 q.log
-rw-r--r-- 1 conan conan  195  8月  2 10:07 sayHello.r
-rw-r--r-- 1 conan conan 2045  8月  2 11:41 script.r
-rw-rw-r-- 1 conan conan 4510  4月 21 13:43 vbox.log
```

模拟/tmp产生临时文件

```{bash}
~ cp files/* tmp/
```

每10秒扫描./tmp目录，并清空文件

```{bash}
~ reap -t 10s ./tmp
  rm /home/conan/nodejs/nodejs-reap/tmp/github.Rproj 303b
  rm /home/conan/nodejs/nodejs-reap/tmp/q.log 821b
  rm /home/conan/nodejs/nodejs-reap/tmp/sayHello.r 195b
  rm /home/conan/nodejs/nodejs-reap/tmp/script.r 2kb
  rm /home/conan/nodejs/nodejs-reap/tmp/vbox.log 4.4kb

  0 files
  0b total

  0 files
  0b total

  rm /home/conan/nodejs/nodejs-reap/tmp/github.Rproj 303b
  rm /home/conan/nodejs/nodejs-reap/tmp/q.log 821b
  rm /home/conan/nodejs/nodejs-reap/tmp/sayHello.r 195b
  rm /home/conan/nodejs/nodejs-reap/tmp/script.r 2kb
  rm /home/conan/nodejs/nodejs-reap/tmp/vbox.log 4.4kb  
```

## 3. reap程序开发

本地安装reap

```{bash}
~ npm install reap
reap@0.1.0 node_modules/reap
├── ms@0.6.1
├── bytes@0.2.1
├── batch@0.4.0
├── debug@0.7.2
└── commander@1.2.0 (keypress@0.1.0)
```

增加文件：app.js，通过nodejs来控制reap.

```{bash}
~ vi app.js

var Reaper = require('reap');

var threshold = 1000;//ms
var reaper = new Reaper({threshold:1000});
reaper.watch('./tmp');
reaper.on('remove', function(file){
    console.log('  rm %s %sb', file.path, file.size);
});

setInterval(function(){
    console.log("\n"+new Date());
    reaper.start(function(err, files){
        if (err) throw err;
        console.log('  %d files\n', files.length);
    });
}, threshold);

```

通过node运行reap, 每1s清理一次。

```{bash}
~ node app.js

Wed Sep 25 2013 12:25:44 GMT+0800 (CST)
  0 files

Wed Sep 25 2013 12:25:45 GMT+0800 (CST)
  rm /home/conan/nodejs/nodejs-reap/tmp/github.Rproj 303b
  rm /home/conan/nodejs/nodejs-reap/tmp/q.log 821b
  rm /home/conan/nodejs/nodejs-reap/tmp/sayHello.r 195b
  rm /home/conan/nodejs/nodejs-reap/tmp/script.r 2045b
  rm /home/conan/nodejs/nodejs-reap/tmp/vbox.log 4510b
  5 files

Wed Sep 25 2013 12:25:46 GMT+0800 (CST)
  0 files


Wed Sep 25 2013 12:25:47 GMT+0800 (CST)
  0 files


Wed Sep 25 2013 12:25:48 GMT+0800 (CST)
  0 files

Wed Sep 25 2013 12:25:49 GMT+0800 (CST)
  rm /home/conan/nodejs/nodejs-reap/tmp/q.log 821b
  rm /home/conan/nodejs/nodejs-reap/tmp/github.Rproj 303b
  rm /home/conan/nodejs/nodejs-reap/tmp/script.r 2045b
  rm /home/conan/nodejs/nodejs-reap/tmp/sayHello.r 195b
  rm /home/conan/nodejs/nodejs-reap/tmp/vbox.log 4510b
  5 files
```


另一个shell 模拟产生临时文件

```{bash}
~ cp files/* tmp/
```

简单的小工具，有时候可以帮我们解决复杂的问题。大型软件架构设计，需要做得更细！！

#### 转载请注明出处：http://blog.fens.me/nodejs-gc-reap/

