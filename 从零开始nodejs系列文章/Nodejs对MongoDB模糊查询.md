Nodejs对MongoDB模糊查询
==============

#### 从零开始nodejs系列文章

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-enviroment/

![Nodejs对MongoDB模糊查询](http://blog.fens.me/wp-content/uploads/2013/07/reg-title.png)

#### 前言

模糊查询是数据库的基本操作之一，实现对给定的字符串是否与指定的模式进行匹配。如果字符完全匹配，可以用=等号表示，如果部分匹配可认为是一种模糊查询。在关系型数据中，通过SQL使用like ‘%fens%’的语法。那么在mongodb中我们应该如何实现模糊查询的效果呢。

#### 目录

1. mongodb模糊查询
2. nodejs通过mongoose的模糊查询

## 1. mongodb模糊查询

我们打开mongodb，以name文字字段进行测试。

### 精确查询

当{‘name’:'未来警察’}时，精确匹配到一条记录。

```{bash}

db.movies.find({'name':'未来警察'})
```

![](http://blog.fens.me/wp-content/uploads/2013/07/reg.png)

### 模糊查询

{‘name’:/未来/}，匹配到了多条记录。

```{bash}
db.movies.find({'name':/未来/})
```
![](http://blog.fens.me/wp-content/uploads/2013/07/reg2.png)

**MongoDB的模糊查询，其实是正则查询的一种。**
注：在关系型数据中，单独有一个关键字like做模糊查询，如果不用like，也可以在关系型数据中使用正则查询。

MongoDB官方介绍：http://docs.mongodb.org/manual/reference/operator/regex/

```{bash}
官方举例：
db.collection.find( { field: /acme.*corp/i } );
db.collection.find( { field: { $regex: 'acme.*corp', $options: 'i' } } );
```

## 2. nodejs通过mongoose的模糊查询

希望实现的效果：

![](http://blog.fens.me/wp-content/uploads/2013/07/reg3.png)

下面说说如何用mongoose进行模糊查询。

使用mongoose访问mongodb，在 Mongoose使用案例–让JSON数据直接入库MongoDB 一文中已经讲过。

我们对Movie建模，并构造dao层。

### 查询所有电影

```{bash}

MovieDAO.prototype.findByName = function(query, callback) {
  Movie.findOne(query, function(err, obj){
    callback(err, obj);
  });
};
```

通过传入query对象，就可以进行查询。

接下来，构造query对象

```{bash}
//代码片断
exports.movie = function(req, res) {
  var query={};
  if(req.query.m2) {
    query['name']=new RegExp(req.query.m2);//模糊查询参数
  }

  Movie.findByName (query,function(err, list){
    return res.render('admin/movie', {movieList:list});
  });
}
```

**请注意，刚才我们已经分析了MongoDB的的模糊查询是通过正则表达式实现的，对应mongodb中，可以直接使用 ‘/../’ 斜杠。**

**但是在nodejs中，必须要使用RegExp，来构建正则表达式对象。**

其实很简单，一层窗户纸。知道了实现原理，一切都变得很容易。

#### 转载请注明出处：http://blog.fens.me/nodejs-mongodb-regexp















