nodejs豆瓣爬虫
================

#### 从零开始nodejs系列文章

从零开始nodejs系列文章，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-crawler-douban/

![nodejs豆瓣爬虫](http://blog.fens.me/wp-content/uploads/2013/06/crawler.jpg)

#### 目录：

1. 类库介绍
2. win7安装jquery – 失败
3. ubuntu安装jQuery – 成功
4. 豆瓣爬虫

## 1. 类库介绍

1. web项目，基于express3, ejs模板
2. 通过request抓取网页
3. 通过jQuery, jsdom, htmlparser提取网页内容

## 2. win7安装node-jquery – 失败

由于win7安装node-jquery的包报错，直接改成ubuntu下开发。
Ubuntu的Nodejs安装请参考：[准备Nodejs开发环境Ubuntu](http://blog.fens.me/nodejs-enviroment/)

```{bash}

~ npm install jquery
D:\workspace\project\moiveme\node_modules\jquery\node_modules\contextify>node "D:\toolkit\nodejs\node_modules\npm\bin\no
de-gyp-bin\\..\..\node_modules\node-gyp\bin\node-gyp.js" rebuild
npm http 304 https://registry.npmjs.org/cssom
npm http GET https://registry.npmjs.org/cssom/-/cssom-0.2.5.tgz
npm http 304 https://registry.npmjs.org/request
npm http 304 https://registry.npmjs.org/cssstyle
npm http GET https://registry.npmjs.org/cssstyle/-/cssstyle-0.2.3.tgz
npm http GET https://registry.npmjs.org/request/-/request-2.21.0.tgz
npm http 200 https://registry.npmjs.org/cssom/-/cssom-0.2.5.tgz
npm http 200 https://registry.npmjs.org/request/-/request-2.21.0.tgz
npm http 200 https://registry.npmjs.org/cssstyle/-/cssstyle-0.2.3.tgz
C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\Microsoft.Cpp.InvalidPlatform.Targets(23,7): error MSB8007: 项目“cont
ex
tify.vcxproj”的平台无效。平台为“x64”。您会看到此消息的可能原因是，您尝试在没有解决方案文件的情况下生成项目，并且为此
项目指定了并不存在的非默认平台。 [D:\workspace\project\moiveme\node_
modules\jquery\node_modules\contextify\build\contextify.vcxproj]
gyp ERR! build error
gyp ERR! stack Error: `C:\Windows\Microsoft.NET\Framework\v4.0.30319\msbuild.exe` failed with exit code: 1
gyp ERR! stack at ChildProcess.onExit (D:\toolkit\nodejs\node_modules\npm\node_modules\node-gyp\lib\build.js:267:23)
gyp ERR! stack at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack at Process.ChildProcess._handle.onexit (child_process.js:784:12)
gyp ERR! System Windows_NT 6.1.7601
gyp ERR! command "node" "D:\\toolkit\\nodejs\\node_modules\\npm\\node_modules\\node-gyp\\bin\\node-gyp.js" "rebuild"
gyp ERR! cwd D:\workspace\project\moiveme\node_modules\jquery\node_modules\contextify
gyp ERR! node -v v0.10.5
gyp ERR! node-gyp -v v0.9.5
gyp ERR! not ok
npm ERR! weird error 1
npm ERR! not ok code 0
```

## 3. ubuntu安装node-jquery

ubuntu安装node-jquery还是有一些安装过程中的问题

1. jsdom的依赖版本问题
2. jQuery非jquery，两个包一字之差，其实是不一样的。

```{bash}
~ sudo npm install jsdom
```

安装可能会有错误，jsdom依赖的问题。修改packages.json文件，指定jsdom版本。

```{bash}
~ vi packages.json
"dependencies": {
"jsdom" : "https://github.com/tmpvar/jsdom/tarball/4cf155a1624b3fb54b2eec536a0c060ec1bab4ab"
}
```

继续安装依赖包，这里请使用jQuery包，不要用jquery会有错误。

```{bash}
~ sudo npm install jQuery
~ sudo npm install xmlhttprequest
~ sudo npm install request
~ sudo npm install htmlparser
```

安装完成。

## 4. 豆瓣爬虫

设计场景：对豆瓣电影进行爬取。

http://movie.douban.com/subject/11529526/

![](http://blog.fens.me/wp-content/uploads/2013/06/1.png)

### 项目目录：

```{bash}
~ pwd
/home/conan/workspace/nodejs/nodejs-demo
~ ll
drwxr-xr-x 6 conan conan 4096 Jun 6 18:02 ./
drwxrwxr-x 4 conan conan 4096 Jun 6 14:19 ../
-rw-rw-r-- 1 conan conan 842 Jun 6 14:19 app.js
drwxr-xr-x 10 conan conan 4096 Jun 6 18:01 node_modules/
-rw-rw-r-- 1 conan conan 283 Jun 6 16:44 package.json
drwxr-xr-x 5 conan conan 4096 Jun 6 14:19 public/
drwxr-xr-x 2 conan conan 4096 Jun 6 15:59 routes/
drwxr-xr-x 2 conan conan 4096 Jun 6 14:19 views/
```

### 增加myUtil.js文件

```{bash}

~ vi myUtil.js
var MyUtil = function () {
};
var http = require('http');
var request = require('request');
MyUtil.prototype.get=function(url,callback){
request(url, function (error, response, body) {
if (!error && response.statusCode == 200) {
callback(body,response.statusCode);
}
})
}
module.exports = new MyUtil();
```

### 修改控制器 routes/index.js

```{bash}
~ vi routes/index.js
var myUtil = require('../myUtil.js');
var $ = require('jQuery');
exports.index = function(req, res){
var url="http://movie.douban.com/subject/11529526";
console.log(url);
myUtil.get(url,function(content,status){
console.log("status:="+status);
res.send(content);
});
};
```

这里我们已经把content内容，输出到了本地程序里。
  打开浏览器：http://192.168.1.104:3000

![](http://blog.fens.me/wp-content/uploads/2013/06/21.png)

### 用jQuery提出红色的文字：

```{bash}
~ vi routes/index.js
var myUtil = require('../myUtil.js');
var $ = require('jQuery');
exports.index = function(req, res){
var url="http://movie.douban.com/subject/11529526";
console.log(url);
myUtil.get(url,function(content,status){
console.log("status:="+status);
var movie={}
movie.name = $(content).find('span[property="v:itemreviewed"]').text();
movie.director = $(content).find('#info span:nth-child(1) a').text();
console.log(movie);
res.send(content);
});
};
```

### 查看server的日志输出

```{bash}
http://movie.douban.com/subject/11529526
status:=200
{ name: '中国合伙人', director: '陈可辛' }
GET / 200 4480ms - 72.55kb
```

通过jQuery的xpath，我们很容易分析出“电影名”和“导演”的字段。

用Nodejs写爬虫代码几有短短几行，比起JAVA动一下就千行的代码量来说，让我们轻松了很多。

效率上，终于可以达到以1人顶100人的目的了。

希望我的介绍对你用处。

#### 转载请注明出处：http://blog.fens.me/nodejs-crawler-douban/











