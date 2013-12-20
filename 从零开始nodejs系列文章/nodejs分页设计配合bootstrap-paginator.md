nodejs分页设计配合bootstrap-paginator
============

#### 从零开始nodejs系列文章

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-enviroment/

#### 前言

分页功能是查询的必备功能，比如我要查询一个多于1000条记录的结果集。如果一次全部展示，不仅浪费带宽，增长等待时间，用户看到这么多的数据也会觉得的头疼。但是有了分页功能，可以根据用户的习惯，一页一页的处理少量的数据，展示少量的数据，就像是在看书一样的。

分页功能非常实用，但是实现起来也稍有复杂。不仅要考虑到前端的展示和传值问题，还要考虑后端面向数据的查询语句。不过我也发现了前端和后端对应的解决的方案。前端程序可以通过[bootstrap-paginator](http://bootstrappaginator.org/)项目来封装，而后端我是自己实现的。本来是准备使用[mongoose-paginate](https://github.com/edwardhotchkiss/mongoose-paginate)项目，但他的设计过于简单，而且与bootstrap-paginator的一些传参习惯不符，自已实现其实更好控制。

![nodejs分页设计配合bootstrap-paginator](http://blog.fens.me/wp-content/uploads/2013/07/page.png)

#### 目录

1. 分页前端雏形bootstrap-paginator
2. 分页后端查询mongoose
3. 分页结果返回页面(后端->前端)
4. 分页查询参数传递(前端->后端)
5. 分页完整展现

## 1. 分页前端雏形bootstrap-paginator

下载bootstrap-paginator：[下载地址](https://github.com/lyonlai/bootstrap-paginator/archive/master.zip)
解压后，把bootstrap-paginator.min.js文件复制到public/js的目录

如果你的项目，已经配置了bootstrap, jquery。 那么你只需要在用到分页的网页上增加一行js包的引用就可以了。

```{bash}
~ vi view/admin/moive.html
<html>
<head>
<link href="/css/bootstrap.min.css" rel="stylesheet" media="screen" />
<script type="text/javascript" src="/js/bootstrap.min.js"></script>
<script type="text/javascript" src="/js/bootstrap-paginator.min.js"></script>
</head>
<body>
<!-- 代码片断 查询表单 -->
<form method="GET">
...
</form>

<!-- 代码片段 分页控件 -->
<div class="span12">
<div id="page1"></div>
</div>

<!-- 代码片段 查询结果 -->
<div class="span12">
<table class="table table-hover table-striped">
...
</table>
</div>

<!-- 代码片段 -->
<script type="text/javascript">
$(function(){

//分页功能
var options = {
currentPage:2,
totalPages:5,
numberOfPages:5
}
$('#page1').bootstrapPaginator(options);

})
</script>

</body>
</html>
```

### 显示效果：

![](http://blog.fens.me/wp-content/uploads/2013/07/p2.png)

## 2. 分页后端查询mongoose

使用mongoose访问mongodb，在 [Mongoose使用案例–让JSON数据直接入库MongoDB](http://blog.fens.me/nodejs-mongoose-json/) 一文中已经讲过。

### 我们构建分布查询的DAO。

+ 这里Movie是model类型，没有用到entity。直接通过model做的分页查询。
+ 分页查询是二步查询，第一步查询结果集数据，第二步查询总记录
+ 通过skip,limit进行分页。（关于skip的效率问题，10W以下的记录，skip性能是可以接受的，10w以上的记录需要根据排序的字段，通过索引定位查询，这个概念以后再讲。）
+ 排序功能默认用插入时间倒叙：sort(‘-create_date’)

### 查询参数：

+ q，查询条件
+ col，数据返回字段
+ pageNumber，当前是第几页，如果不存在默认为第1页
+ resultsPerPage，每页多少条记录

### 分页的返回值

+ null：空错误，因为错误已经通过if处理了
+ pageCount：一共有多少页
+ results：数据结果集

```{bash}

~ vi model/Movie.js

var Movie = mongodb.mongoose.model("Movie", MovieSchema);
var MovieDAO = function(){};

//代码片段

MovieDAO.prototype.findPagination = function(obj,callback) {
  var q=obj.search||{}
  var col=obj.columns;

  var pageNumber=obj.page.num||1;
  var resultsPerPage=obj.page.limit||10;

  var skipFrom = (pageNumber * resultsPerPage) - resultsPerPage;
  var query = Movie.find(q,col).sort('-create_date').skip(skipFrom).limit(resultsPerPage);

  query.exec(function(error, results) {
    if (error) {
      callback(error, null, null);
    } else {
      Movie.count(q, function(error, count) {
        if (error) {
          callback(error, null, null);
        } else {
          var pageCount = Math.ceil(count / resultsPerPage);
          callback(null, pageCount, results);
        }
      });
    }
  });
}
```

## 3. 分页结果返回页面(后端->前端)

### 传参数到页面：page对象

+ limit:5，每页限制5条记录
+ num:1，查询的页面
+ pageCount，一共有多少页
+ size，当前页面有多少条记录
+ numberOf，分页用几个标签显示

```{bash}

~ vi routes/moive.js

//代码片段
exports.movie = function(req, res) {
var search={};
var page={limit:5,num:1};

//查看哪页
if(req.query.p){
page['num']=req.query.p<1?1:req.query.p;
}

var model = {
search:search,
columns:'name alias director publish images.coverSmall create_date type deploy',
page:page
};

Movie.findPagination(model,function(err, pageCount, list){
page['pageCount']=pageCount;
page['size']=list.length;
page['numberOf']=pageCount>5?5:pageCount;

return res.render('admin/movie', {
title:'电影|管理|moive.me',
page:'admin',nav:'admin.movie',
movieList:list,
page:page
});
});
}
```

## 4. 分页查询参数传递(前端->后端)

### 前端向后端：参数传递过程

+ 通过javascript，从页面向控制器传参数p.
+ 通过composeUrlParams函数，封装原表单的查询参数
+ 通过pageUrl函数，拼接带分布的url请求

### 后端向前端：参数传递过程

+ 如果page1的div增加自定义属性
+ 在页面渲染时，通过js解析div增加自定义属性，赋值给bootstrap-paginator控件

```{bash}
~ vi view/admin/moive.html

//修改分页的div，增加自定义的属性
<div class="span12">
<div id="page1" pageCount="<%=page.pageCount%>" pageNum="<%=page.num %>" pageSize="<%=page.size%>" pageLimit="<%=page.limit%>" numberOfPages="<%=page.numberOf%>"></div>

<!-- 代码片段 -->
<script type="text/javascript">
$(function(){

//获得浏览器参数
$.extend({
getUrlVars: function(){
var vars = [], hash;
var hashes = window.location.href.slice(window.location.href.indexOf('?') + 1).split('&');
for(var i = 0; i < hashes.length; i++){
hash = hashes[i].split('=');
vars.push(hash[0]);
vars[hash[0]] = hash[1];
}
return vars;
},
getUrlVar: function(name){
return $.getUrlVars()[name];
}
});

//封装浏览器参数
var composeUrlParams=function(){
var param='';
$.each($.getUrlVars(), function(i, item) {
if(item!='p'){
var val=$.getUrlVar(item);
if(val) param += "&" + item+"="+val;
}
});
return param;
}

//分页功能
var page=$('#page1');
var options = {
currentPage:page.attr('pageNum'),
totalPages:page.attr('pageCount'),
numberOfPages:page.attr('numberOfPages'),
pageUrl: function(type, page, current){
return "/admin/movie?"+composeUrlParams()+"&p="+page;
}
}
$('#page1').bootstrapPaginator(options);
})
</script>
```

## 5. 分页完整展现

![](http://blog.fens.me/wp-content/uploads/2013/07/p1.png)

分页功能可能实现起来稍有复杂，如果可以完整地封装成一个控件，就会变得非常简单了。不知道是否已经有人做好了这个控件。

如果有时间，我可能也会写一下。希望能帮助到对分页还陌生的朋友。

#### 转载请注明出处：http://blog.fens.me/nodejs-bootstrap-paginator/

























