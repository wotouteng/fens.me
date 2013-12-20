Mongoose使用案例–让JSON数据直接入库MongoDB
==================

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-enviroment/

#### 程序代码已经上传到github有需要的同学，自行下载。
https://github.com/bsspirit/nodejs-demo

本文重点介绍web前端通过JQuery发起POST提交JSON数据，通过Mongoose直接插入或更新到MongoDB。

工程目录沿用nodejs-demo，增加/mongoose路径及对应文件。

关于nodejs-demo项目介绍，请参考文章：

#### Nodejs开发框架Express3.0开发手记–从零开始
http://blog.fens.me/nodejs-express3/

![Mongoose使用案例–让JSON数据直接入库MongoDB](http://blog.fens.me/wp-content/uploads/2013/05/nodejs2.png)

#### 前言

为什么用Nodejs？为什么用MongoDB？从领域语言和代码简洁之道来看，这是我非常关心的问题。

Nodejs基于Javascript，MongoDB脚步同样也是基于Javascript。而且他们的数据存储格式都是JSON，这就是为什么要把他们放在一起的原因了。如果程序前后端能直接处理JSON，我想数据处理过程又可以极大的减化了，代码量又将低少1/5。多么的兴奋啊！让我们来动手验证一下想法吧。

#### 文章目录

1. 配置Mongoose
2. 创建目录及文件
3. 插入数据，POST提交JSON增加一条记录
4. 查询数据，取出刚增加的记录

## 1. 配置Mongoose

增加mongoose的类库

```{bash}

cd d:/workspace/project/nodejs-demo
npm install mongoose

D:\workspace\project\nodejs-demo\node_modules\mongoose\node_modules\mongodb\node_modu
C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\Microsoft.Cpp.InvalidPlatform.Targe
e_modules\mongodb\node_modules\bson\build\bson.vcxproj]
mongoose@3.6.10 node_modules\mongoose
├── muri@0.3.1
├── hooks@0.2.1
├── sliced@0.0.3
├── mpath@0.1.1
├── ms@0.1.0
├── mpromise@0.2.1 (sliced@0.0.4)
└── mongodb@1.3.3 (kerberos@0.0.2, bson@0.1.8)
```

安装时，有64位兼容性错误提示没关系，Mongoose类库安装完成。

### 增加models目录
mkdir models

### 在models目录，增加mongodb.js文件

```{bash}
var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/nodejs');
exports.mongoose = mongoose;
```

指定Mongo的数据库名为nodejs

## 2.创建目录及文件

在models目录，增加数据模型Movie.js

```{bash}
var mongodb = require('./mongodb');
var Schema = mongodb.mongoose.Schema;
var MovieSchema = new Schema({
name : String,
alias : [String],
publish : Date,
create_date : { type: Date, default: Date.now},
images :{
coverSmall:String,
coverBig:String,
},
source :[{
source:String,
link:String,
swfLink:String,
quality:String,
version:String,
lang:String,
subtitle:String,
create_date : { type: Date, default: Date.now }
}]
});
var Movie = mongodb.mongoose.model("Movie", MovieSchema);
var MovieDAO = function(){};
module.exports = new MovieDAO();
```

指定Mongo的数据库集为Movie

数据类型，包括了String,Date,Array,Mixed]

### 打开app.js增加访问路径

```{bash}
var express = require('express')
, routes = require('./routes')
, user = require('./routes/user')
, movie = require('./routes/movie')
, http = require('http')
, path = require('path')
, ejs = require('ejs')
, SessionStore = require("session-mongoose")(express);
...
app.get('/movie/add',movie.movieAdd);//增加
app.post('/movie/add',movie.doMovieAdd);//提交
app.get('/movie/:name',movie.movieAdd);//编辑查询
app.get('/movie/json/:name',movie.movieJSON);//JSON数据
```

### 在routes目录，增加movie.js

```{bash}
var Movie = require('./../models/Movie.js');
exports.movieAdd = function(req, res) {
if(req.params.name){//update
return res.render('movie', {
title:req.params.name+'|电影|管理|moive.me',
label:'编辑电影:'+req.params.name,
movie:req.params.name
});
} else {
return res.render('movie',{
title:'新增加|电影|管理|moive.me',
label:'新增加电影',
movie:false
});
}
};
exports.doMovieAdd = function(req, res) {
res.send({'success':true});
};
```

### 在views目录，增加movie.html

```{bash}
<% include header.html %>
<div class="container-fluid">
<div class="row-fluid">
<div class="span8">
<form>
<fieldset>
<legend><%=label%></legend>
<textarea id="c_editor" name="c_editor" class="span12" rows="10"></textarea>
<button id="c_save" type="button" class="btn btn-primary">保存</button>
</fieldset>
<form>
</div>
</div>
</div>
<% include footer.html %>
```

网页效果：http://localhost:3000/movie/add

![](http://blog.fens.me/wp-content/uploads/2013/05/movieInsert.png)

## 3. 插入数据，POST提交JSON增加一条记录

基础环境，都搭建好后，我们开台准备向mongodb中插入数据。

首先创建一个json数据文件，这样我们可以方便点，直接读入这个文件，创建JSON数据对象了。

### 在public/javascripts/目录，增加movie.json文件

```{bash}

{
"name": "未来警察",
"alias": ["Future X-Cops ","Mei loi ging chaat"],
"publish": "2010-04-29",
"images":{
"coverBig":"/img/movie/1_big.jpg",
"coverSmall":"/img/movie/1_small.jpg"
},
"source":[{
"source":"优酷",
"link":"http://www.youku.com",
"swfLink":"http://player.youku.com/player.php/sid/XMTY4NzM5ODc2/v.swf",
"quality":"高清",
"version":"正片",
"lang":"汉语",
"subtitle":"中文字幕"
},{
"source":"搜狐",
"link":"http://tv.sohu.com",
"swfLink":"http://share.vrs.sohu.com/75837/v.swf&topBar=1&autoplay=false&plid=3860&pub_catecode=",
"quality":"高清",
"version":"正片",
"lang":"汉语",
"subtitle":"中文字幕"
}]
}
```

### 在public/javascripts/目录，增加jquery.json-2.4.js类库

```{bash}
<script src=”/javascripts/jquery-1.9.1.min.js”></script>
<script src=”/javascripts/bootstrap.min.js”></script>
<script src=”/javascripts/jquery.json-2.4.js”></script>
<script src=”/javascripts/movie.js”></script>
</body>
</html>
```

### 在public/javascripts/目录，增加movie.js文件，作为前端脚本

```{bash}
$(function() {
var mdata={};
var url = '/javascripts/movie.json';
$.getJSON(url, function(data) {
mdata=data;
render_editor_form(mdata);
render_event_form(mdata);
});
var render_editor_form=function(data){
$('#c_editor').val($.toJSON(data));
};
var render_event_form=function(){
$('#c_save').on('click',function(event){
var data = {};
data['content'] = mdata;
$.ajax({
type: "POST",
url: '/movie/add',
data: data,
success: function (data, textStatus){
if(data.success){
$('#msg').html('成功保存!');
$('#msg').addClass('alert alert-success');
$(location).attr('href','/movie/'+mdata.name);
} else {
$('#msg').html(data.err);
$('#msg').addClass('alert alert-error');
}
}
});
});
};
});
```

### 修改views/footer.html，增加movie.js文件引用，同时增加jquery.json包

```{bash}
<script src="/javascripts/jquery-1.9.1.min.js"></script>
<script src="/javascripts/bootstrap.min.js"></script>
<script src="/javascripts/jquery.json-2.4.js"></script>
<script src="/javascripts/movie.js"></script>
</body>
</html>
```

网页效果：http://localhost:3000/movie/add

![](http://blog.fens.me/wp-content/uploads/2013/05/movieInsert2.png)

### 在models/Movie.js，增加save方法

```{bash}
MovieDAO.prototype.save = function(obj, callback) {
var instance = new Movie(obj);
instance.save(function(err){
callback(err);
});
};
```

在routes/movie.js，调用save方法

```{bash}
exports.doMovieAdd = function(req, res) {
console.log(req.body.content);
var json = req.body.content;
if(json._id){//update
} else {//insert
Movie.save(json, function(err){
if(err) {
res.send({'success':false,'err':err});
} else {
res.send({'success':true});
}
});
}
};
```

### 控制台日志

```{bash}
Express server listening on port 3000
{ name: '未来警察',
alias: [ 'Future X-Cops ', 'Mei loi ging chaat' ],
publish: '2010-04-29',
images:
{ coverBig: '/img/movie/1_big.jpg',
coverSmall: '/img/movie/1_small.jpg' },
source:
[ { source: '优酷',
link: 'http://www.youku.com',
swfLink: 'http://player.youku.com/player.php/sid/XMTY4NzM5ODc2/v.swf',
quality: '高清',
version: '正片',
lang: '汉语',
subtitle: '中文字幕' },
{ source: '搜狐',
link: 'http://tv.sohu.com',
swfLink: 'http://share.vrs.sohu.com/75837/v.swf&topBar=1&autoplay=false&plid=3860&pub_ca
quality: '高清',
version: '正片',
lang: '汉语',
subtitle: '中文字幕' } ] }
POST /movie/add 200 57ms - 21b
```

### 数据已插入MongoDB

![](http://blog.fens.me/wp-content/uploads/2013/05/movieInsert3.png)

## 4. 查询数据，取出刚增加的记录

### models/Movie.js,增加findByName方法

```{bash}
MovieDAO.prototype.findByName = function(name, callback) {
Movie.findOne({name:name}, function(err, obj){
callback(err, obj);
});
};
```

### routes/movies.js,增加movieJSON

```{bash}

exports.movieJSON = function(req, res) {
Movie.findByName(req.params.name,function(err, obj){
res.send(obj);
});
}
```

### 前端javascripts/movie.js，从/movie/json/xxx处取数据

```{bash}
var mdata={};
var url = '/javascripts/movie.json';
var movie=$('#c_editor').attr('movie')
if(movie){
url = '/movie/json/'+movie;
}
```

### 修改 views/movie.html

```{bash}
<textarea id=”c_editor” name=”c_editor” rows=”10″ <%= (movie?’”movie=’+movie+’”‘:”) %>></textarea>
```

### 访问我们的网页

http://localhost:3000/movie/未来警察

![](http://blog.fens.me/wp-content/uploads/2013/05/movieInsert4.png)

数据从/movie/json/未来警察，处读取。完成尝试。

修改操作与插入的操作类似，我就不做演示了。

希望此文对大家有所帮助。

#### 以上程序代码，我已经上传到github有需要的同学，自行下载。
https://github.com/bsspirit/nodejs-demo

#### 转载请注明出处：http://blog.fens.me/nodejs-mongoose-json/









