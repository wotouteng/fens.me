Nodejs与R跨平台通信
==========

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-rserve-nodejs/

![Nodejs与R跨平台通信](http://blog.fens.me/wp-content/uploads/2013/08/rserve-nodejs.png)

#### 前言

做web开发还没有用过Nodejs的同学，你们已经落后了。Nodejs是基于Javascript实现的一款后台程序开发平台，以我个人的体验开发效率比PHP还要高一些，完全异步加载性能一点也不落后，假以时日还有赶超趋势。

HTML5做为web前端大量使用Javascript，各种炫效果层出不穷。如果能把R语言的效果图，都用HTML5重新做渲染，并增加时时通信和用户交互，两种语言各取优势，必然产生惊为天人的效果。

长话短说，今天介绍 Nodejs与R跨平台通信

#### 目录

1. Nodejs简单介绍
2. R语言配置环境
3. Nodejs配置环境
4. Nodejs与R跨平台通信

## 1. Nodejs简单介绍

Node.js是一个可以快速构建网络服务及应用的平台，基于Chrome’s JavaScript runtime，也就是说，实际上它是对GoogleV8引擎（应用于Google Chrome浏览器)进行了封装。V8引擎执行Javascript的速度非常快，性能非常好。Node对一些特殊用例进行了优化，提供了替代的API，使得V8在非浏览器环境下运行得更好。

## 2. R语言配置环境

本文中介绍的 Nodejs与R的跨平台通信，对于R语言的支持库是Rserve，通过Rserve提供一个R的TCP/IP的通信协议，实现Nodejs和R的通信。关于如何配置Rserve，请参考文章：Rserve与Java的跨平台通信

我已经在Linux配置好的Rserve环境，进程号9736，端口6311

```{bash}
~ ps -aux|grep Rserve
conan     9736  0.0  1.2 116288 25440 ?        Ss   13:11   0:01 /usr/lib/R/bin/Rserve --RS-enable-remote

~ netstat -nltp|grep Rserve
tcp        0      0 0.0.0.0:6311            0.0.0.0:*               LISTEN      9736/Rserve
```

## 3. Nodejs配置环境

对于Nodejs环境，也比较简单，以express3库作为基础web框架，rio是让Nodejs与Rserve通信的依赖库。如果是Nodejs的新手从头学习，请参考系列文章：[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，如果仅仅搭建一个Nodejs测试环境，请参考文章：[准备Nodejs开发环境Ubuntu](http://blog.fens.me/nodejs-enviroment)

现在我们已创建好了Express3的项目(开发环境Win7)

项目目录：D:\workspace\project\investment\webui

```{bash}

~ D:\workspace\project\investment\webui>ls
README.md  app.js  models  node-rio-dump.bin  node_modules  package.json  public  routes  views
```

### 安装rio库

```{bash}

~ D:\workspace\project\investment\webui>npm install rio
npm http GET https://registry.npmjs.org/rio
npm http 200 https://registry.npmjs.org/rio
npm http GET https://registry.npmjs.org/rio/-/rio-0.9.0.tgz
npm http 200 https://registry.npmjs.org/rio/-/rio-0.9.0.tgz
npm http GET https://registry.npmjs.org/hexy
npm http GET https://registry.npmjs.org/binary
npm http 200 https://registry.npmjs.org/binary
npm http 200 https://registry.npmjs.org/hexy
npm http GET https://registry.npmjs.org/binary/-/binary-0.3.0.tgz
npm http GET https://registry.npmjs.org/hexy/-/hexy-0.2.5.tgz
npm http 200 https://registry.npmjs.org/hexy/-/hexy-0.2.5.tgz
npm http 200 https://registry.npmjs.org/binary/-/binary-0.3.0.tgz
npm http GET https://registry.npmjs.org/chainsaw
npm http GET https://registry.npmjs.org/buffers
npm http 200 https://registry.npmjs.org/buffers
npm http 200 https://registry.npmjs.org/chainsaw
npm http GET https://registry.npmjs.org/buffers/-/buffers-0.1.1.tgz
npm http GET https://registry.npmjs.org/chainsaw/-/chainsaw-0.1.0.tgz
npm http 200 https://registry.npmjs.org/buffers/-/buffers-0.1.1.tgz
npm http 200 https://registry.npmjs.org/chainsaw/-/chainsaw-0.1.0.tgz
npm http GET https://registry.npmjs.org/traverse
npm http 200 https://registry.npmjs.org/traverse
npm http GET https://registry.npmjs.org/traverse/-/traverse-0.3.9.tgz
npm http 200 https://registry.npmjs.org/traverse/-/traverse-0.3.9.tgz
rio@0.9.0 node_modules\rio
├── hexy@0.2.5
└── binary@0.3.0 (buffers@0.1.1, chainsaw@0.1.0)
```

## 4. Nodejs与R跨平台通信

下面我们开始写程序，来实现跨平台的通信。

在app.js配置文件中，增加一个路由路径

```{bash}
~ vi app.js
//代码有省略
var vis = require('./routes/vis')
app.get('/vis/rio',vis.rio);
```

在路由目录/routes增加一个路由文件vis.js

```{bash}
~ vi /routes/vis.js

var rio = require("rio");
exports.rio = function(req, res){
	options = {
		host : "192.168.1.201",
		port : 6311,
        callback: function (err, val) {
            if (!err) {
            	console.log("RETURN:"+val);
            	return res.send({'success':true,'res':val});
            } else {
            	console.log("ERROR:Rserve call failed")
            	return res.send({'success':false});
            }
        },
    }
    rio.enableDebug(true);//开启调试模式
    rio.evaluate("pi / 2 * 2 * 2",options);//运行R代码
};
```

在上面代码中，我们实现了rio与rserve的远程连接。以R的命令(pi / 2 * 2 * 2)作为参数，传给Rserve。通过callback的方法，获得R的返回值。

### 在浏览器中测试：

http://localhost:3000/vis/rio

在浏览器中返回：是一个json格式的对象

```{bash}
{
  "success": true,
  "res": 6.283185307179586
}
```

### 命令行日志

```{bash}
Connected to Rserve
Supported capabilities --------------

Sending command to Rserve
00000000: 0300 0000 1400 0000 0000 0000 0000 0000  ................
00000010: 0410 0000 7069 202f 2032 202a 2032 202a  ....pi./.2.*.2.*
00000020: 2032 0001                                .2..

Data packet
00000000: 2108 0000 182d 4454 fb21 1940            !....-DT{!.@

Type SEXP 33
Response value: 6.283185307179586
RETURN:6.283185307179586
GET /vis/rio 200 33ms - 49b
Disconnected from Rserve
Closed from Rserve
```

我可以看到nodejs与Rserve的通信情况，Response value: 6.283185307179586，与页面上面是一致的。

### 接下来，我们改一下R的运行命令：

rnorm(10)，取10个符合N(0,1)正态分布的随机数

```{bash}
rio.evaluate("rnorm(10)",options);//运行R代码
```

### 浏览器返回结果

```{bash}

{
  "success": true,
  "res": [
    -0.011531884725262991,
    0.5106443501593562,
    -0.05216533321965309,
    1.9221980152236238,
    0.5205238122633465,
    -0.3275367539102907,
    -0.06588102930129405,
    1.5410418730008988,
    1.308169913050071,
    0.005044179478212583
  ]
}
```

命令行日志

```{bash}
Connected to Rserve
Supported capabilities --------------

Sending command to Rserve
00000000: 0300 0000 1000 0000 0000 0000 0000 0000  ................
00000010: 040c 0000 726e 6f72 6d28 3130 2900 0101  ....rnorm(10)...

Data packet
00000000: 2150 0000 f6ca 0c5e 079e 87bf 9b4a fad1  !P..vJ.^...?.JzQ
00000010: 3257 e03f eda2 5320 6ab5 aabf 2b25 bdb4  2W`?m"S.j5*?+%=4
00000020: 52c1 fe3f ebba ce8d 21a8 e03f bc17 92b7  RA~?k:N.!(`?<..7
00000030: 5cf6 d4bf ca9f 4642 94dd b0bf 1be3 e485  \vT?J.FB.]0?.cd.
00000040: 1ba8 f83f 5a94 2293 43ee f43f 1724 4e9e  .(x?Z.".Cnt?.$N.
00000050: 34a9 743f                                4)t?

Type SEXP 33
Response value: -0.011531884725262991,0.5106443501593562,-0.05216533321965309,1.9221980152236238,0.5205238122633465,-0.3
275367539102907,-0.06588102930129405,1.5410418730008988,1.308169913050071,0.005044179478212583
RETURN:-0.011531884725262991,0.5106443501593562,-0.05216533321965309,1.9221980152236238,0.5205238122633465,-0.3275367539
102907,-0.06588102930129405,1.5410418730008988,1.308169913050071,0.005044179478212583
GET /vis/rio 200 30ms - 285b
Disconnected from Rserve
Closed from Rserve
```

我们实现了Nodejs与R跨平台通信，有点感觉像打通了中国功夫的“任督二脉”，从此以后，成为一代高手。

#### 转载请注明出处：http://blog.fens.me/r-rserve-nodejs/

