Nodejs配合bootstrap-select下拉列表
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

网页里的下位列表，英文名drop down list，几乎所有网站都会用到这个组件。bootstrap已经设计了一个下拉列表的基本样式，在表单中展示已经够用了，但如果我们希望有更多一些的功能，不妨学一下Bootstrap-select，[github项目地址](https://github.com/silviomoreto/bootstrap-select) ，[官方demo](http://silviomoreto.github.io/bootstrap-select/)。

#### 目录

1. 功能需求列表
2. 代码实现
3. 使用bootstrap-select实现需求

#### 我将通过一个例子进行演示

![Nodejs配合bootstrap-select下拉列表](http://blog.fens.me/wp-content/uploads/2013/07/dropdwon1.png)

## 1. 功能需求

如图所示，表单中“类型”使用下拉列表

### 有如下的显示要求：

+ 默认显示 “请选择”
+ 默认值是空
+ 单选下拉列表
+ 下拉后选中的行，结尾以“√”标识
+ 下拉列表长度为5行，多于5行的用滚动条显示
+ 通过nodejs传参数设置默认值

## 2. 代码实现

### 使用bootstrap

nodejs中使用bootstrap，在 [Nodejs开发框架Express3.0开发手记–从零开始](http://blog.fens.me/nodejs-express3/) 一文中已经有介绍。

### 引入bootstrap-select类库

下载类库：https://github.com/silviomoreto/bootstrap-select/zipball/master

解压后，使用下面2个文件：
+ bootstrap-select.min.js，放到public/js目录
+ bootstrap-select.min.css，放到public/css目录

### 在view/admin/moive.html中引用这两个文件，(代码片段)

```{bash}
~ vi view/admin/moive.html
<html>
<head>
<link href="/css/bootstrap.min.css" rel="stylesheet" media="screen" />
<link href="/css/bootstrap-select.min.css" rel="stylesheet" media="screen" />

<script type="text/javascript" src="/js/bootstrap.min.js"></script>
<script type="text/javascript" src="/js/bootstrap-select.min.js"></script>
</head>
<body>
<!-- 代码片断 -->
<form class="form-horizontal well" method="GET">
<select name="type" class="selectpicker show-tick" data-size="5" value="<%=search.type||''%>">
<option></option>
<option>科幻</option>
<option>剧情</option>
<option>犯罪</option>
<option>西部</option>
<option>爱情</option>
</select>
</form>

<!-- 代码片段 -->
<script type="text/javascript">
$(function(){

//下拉菜单
$('.selectpicker').selectpicker({noneSelectedText:'请选择'});
$('.selectpicker').selectpicker('val',$('.selectpicker').attr('value'));

})
</script>

</body>
</html>
```

## 3. 使用bootstrap-select实现需求

通过select标签实现下拉列表

### 初始化样式：
+ 通过class=”selectpicker”，设置bootstrap-select的样式。
+ 通过$(‘.selectpicker’).selectpicker()，启动js渲染

### 默认显示 “请选择”

```{bash}
$('.selectpicker').selectpicker({noneSelectedText:'请选择'});
```

### 默认值是空

```{bash}
<option></option>
```

### 单选下拉列表

+ 无特别代码。
+ 多选下拉列表，增加multiple属性

```{bash}
<select class="selectpicker" multiple >
```

### 下拉后选中的行，结尾以“√”标识

增加show-tick的css显示类型

```{bash}
<select class="selectpicker show-tick" >
```

### 下拉列表长度为5行，多于5行的用滚动条显示

增加data-size=”5″的属性

```{bash}
<select class="selectpicker" data-size="5" >
```

### 通过nodejs传参数设置默认值

增加value属性，获得nodejs的传参

```{bash}
<select name="type" class="selectpicker show-tick" data-size="5" value="<%=search.type||''%>">
```

再通过javascript前端赋值

```{bash}
$('.selectpicker').selectpicker('val',$('.selectpicker').attr('value'));
```

### 实现下拉列表的需求功能：

![](http://blog.fens.me/wp-content/uploads/2013/07/dropdwon2.png)

#### 转载请注明出处：http://blog.fens.me/nodejs-bootstrap-select/





















