R语言构建websocket服务器
==========

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-websocket-websockets/

![R语言构建websocket服务器](http://blog.fens.me/wp-content/uploads/2013/12/websockets-r.png)

#### 前言

R语言从一门统计语言，正向着工业化语言发展。不仅支持web的可视化，web的基本操作，还支持websocket。我们的互联网应用程序不用再绕道Rserve，直接通过websocket协议，就能实现与R语言的交互。

R语言正在发生着技术革命，更先进，更便捷….

#### 目录

1. websockets介绍
2. websockets安装
3. websockets的API介绍
4. 快速启动websockets服务器demo
5. R语言创建websocket服务器实例
6. R语言创建websocket客户端连接
7. 用浏览器HTML5原生API客户端连接

## 1. websockets介绍

Websocket协议是基于HTML5规范的，在浏览器上实现的客户端和服务器端通信协议。

### Websocket有以下优势：

* 显着降低网络开销。
* 减少服务器的处理开销。
* 简化Web客户端（推）的快速异步更新。
* 简化服务器和客户端之间的耦合状态。

websockets包，是R语言的一个websocket接口的类库。通过websockets包，可以非常简单地使用R语言构建一个websocket服务器实例。同时，websockets包还提供客户端的API。

websockets包的发布页：http://cran.r-project.org/web/packages/websockets/index.html

## 2. websockets安装

### 系统环境：

* Linux: Ubuntu Server 12.04.2 LTS 64bit
* R: 3.0.1 x86_64-pc-linux-gnu
* RStudio Server online

注：经过测试websockets在win环境中，有各种的问题。请使用linux环境。

### websockets安装，加载

```{bash}

~ R

> install.packages("websockets")
also installing the dependency ‘caTools’

trying URL 'http://mirror.bjtu.edu.cn/cran/bin/windows/contrib/3.0/caTools_1.16.zip'
Content type 'application/zip' length 227507 bytes (222 Kb)
opened URL
downloaded 222 Kb

trying URL 'http://mirror.bjtu.edu.cn/cran/bin/windows/contrib/3.0/websockets_1.1.7.zip'
Content type 'application/zip' length 272593 bytes (266 Kb)
opened URL
downloaded 266 Kb

package ‘caTools’ successfully unpacked and MD5 sums checked
package ‘websockets’ successfully unpacked and MD5 sums checked

> library(websockets)
'websockets'R3.0.2
```

websockets库依赖于caTools库，caTools是一个工具集，请参考文章：[caTools一个奇特的工具集](http://blog.fens.me/r-catools/)

## 3. websockets的API介绍

* create_server: 创建一个websocket服务器实例，并绑定端口
* daemonize: 绑定websocket服务器实例守护进程，到R的控制台，不支持Windows
* http_response: 发送HTTP Response请求到socket
* http_vars: 解析HTTP GET/POST参数列表
* service: 注册websocket实例的服务队列
* set_callback: 在websocket实例中，定义R函数
* static_file_service: 静态文件
* static_text_service: 静态文本
* websocket: 创建一个websocket客户端实例
* websocket_broadcast: 向注册在同一个websocket服务器实例的客户端发广播
* websocket_close: 关闭客户端连接
* websocket_write: 通过websocket进行数据传输

## 4. 快速启动websockets服务器demo

websockets包，提供了一个demo。通过demo(websockets)函数，直接启动一个简单的websocket服务器。

```{bash}
~ R

> library(websockets)
'websockets'R3.0.2

> demo(websockets)

        demo(websockets)
        ---- ~~~~~~~~~~

Type     to start :

> # Simple example
> require(websockets)

> w = create_server()

> f = function(DATA, WS, ...)
+ {
+   cat("Receive callback\n")
+   D = ""
+   if(is.raw(DATA)) D = rawToChar(DATA)
+   websocket_write(DATA=paste("You sent",D,"\n",collapse=" "),WS=WS)
+ }

> set_callback('receive',f,w)

> cl = function(WS)
+ {
+   cat("Websocket client socket ",WS$socket," has closed.\n")
+ }

> set_callback('closed',cl,w)

> es = function(WS)
+ {
+   cat("Websocket client socket ",WS$socket," has been established.\n")
+ }

> set_callback('established',es,w)

> cat("Direct your web browser to http://localhost:7681\n")
Direct your web browser to http://localhost:7681

> while(TRUE) service(w)
```

打开浏览器：http://192.168.1.201:7681

![](http://blog.fens.me/wp-content/uploads/2013/12/websockets-demo.png)

服务器日志：

```{bash}
Websocket client socket  20  has closed.
Websocket client socket  8  has been established.
Websocket client socket  21  has closed.
```

查看服务器端:

```{bash}
~ netstat -nltp|grep r

Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:7681            0.0.0.0:*               LISTEN      2231/rsession
```

## 5. R语言创建websocket服务器实例

* 1. 加载类库
* 2. 初始化create_server()
* 3. 定义回调函数
* 4. 定义事件服务

```{bash}

~ R

#加载类库
library(websockets)

#浏览器的HTTP输出
text = "<html><body>
<h1>Hello world</h1>
</body></html>"

## 创建服务实例
w = create_server(port=7681,webpage=static_text_service(text))

## 监听receive
recv = function(DATA, WS, ...){
  cat("Receive callback\n")
  D = ""
  if(is.raw(DATA)){D = rawToChar(DATA)}

  cat("Callback:You sent",D,"\n")
  websocket_write(DATA=paste("You sent",D,"\n",collapse=" "),WS=WS)
}
set_callback('receive',recv,w)

## 监听closed
cl = function(WS){
  cat("Websocket client socket ",WS$socket," has closed.\n")
}
set_callback('closed',cl,w)

## established
es = function(WS){
  cat("Websocket client socket ",WS$socket," has been established.\n")
}
set_callback('established',es,w)

#对所有的连接进行监听
while(TRUE) service(w)
```

## 6. R语言创建websocket客户端连接

### 在Linux环境中，新建一个文件：client.r

```{bash}
~ vi client.r

#加载类库
library(websockets)

#创建客户端实例
client = websocket("ws://192.168.1.201",port=7681)

#监听receive
rece<-function(DATA, WS, HEADER) {   D=''   if(is.raw(DATA)){     cat("raw data")     D = rawToChar(DATA)   }   cat("==>",D,"\n")
}
set_callback("receive",rece, client)

#向服务器发请求
websocket_write("2222", client)

#输出服务器的返回值
service(client)

#关闭连接
websocket_close(client)
```

### 运行程序：

```{bash}
> library(websockets)
>
> client = websocket("ws://192.168.1.201",port=7681)
>
> # receive
> rece<-function(DATA, WS, HEADER) { +   D='' +   if(is.raw(DATA)){ +     cat("raw data") +     D = rawToChar(DATA) +   } +   cat("==>",D,"\n")
+ }
> set_callback("receive",rece, client)
> websocket_write("2222", client)
[1] 1
>
> service(client)
raw data==> You sent 2222

>
> websocket_close(client)
Client socket 3  was closed.
```

## 7. 用浏览器HTML5原生API客户端连接

打开浏览器：http://192.168.1.201:7681/

![](http://blog.fens.me/wp-content/uploads/2013/12/websockets-html5-js.png)

我们可以通过浏览器console写js的代码，实现和websocket服务器的通信。

原生的HTML5程序

```{bash}
var ws = new WebSocket("ws://192.168.1.201:7681");
ws.onopen = function(){
console.log("connecting");
};
ws.onmessage = function(message){
console.log(message.data);
console.log(message);
};
function postToServer(msg){
ws.send(msg);
}
function closeConnect(){
ws.close();
console.log("closed");
}

postToServer('browser');
closeConnect();
```

我们这样就完成了，R语言构建的websocket服务器测试。又给R语言与其他语言通信打开了一条便利的通道。

关于websocket的其他语言实现，请参考文章：

* [Java现实WebSocket](http://blog.fens.me/java-websocket-intro/)
* [Nodejs实现websocket的4种方式](http://blog.fens.me/nodejs-websocket/)
* [Socket.io在线聊天室](http://blog.fens.me/nodejs-socketio-chat/)
* [websocket服务器监控](http://blog.fens.me/nodejs-websocket-monitor/)

#### 转载请注明出处：http://blog.fens.me/r-websocket-websockets/


