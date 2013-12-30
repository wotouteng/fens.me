AngularJS路由和模板
===========

[AngularJS体验式编程系列文章](http://blog.fens.me/series-angular/)，将介绍如何用angularjs构建一个强大的web前端系统。angularjs是由Google团队开发的一款非常优秀web前端框架。在当前如此多的web框架下，angularjs能脱颖而出，从架构设计上就高人一等，双向数据绑定，依赖注入，指令，MVC，模板。Angular.js创新地把后台技术融入前端开发，扫去jQuery一度的光芒。用angularjs就像写后台代码，更规范，更结构化，更可控。

#### 关于作者

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/angularjs-route-template/

![AngularJS路由和模板](http://blog.fens.me/wp-content/uploads/2013/08/AngularJS-route.png)

#### 前言

如果想开发一款类似gmail的web应用，我们怎么做呢？

以jQuery的思路，做响应式的架构设计时，我们要监听所有点击事件，通过事件函数触发我们加载数据，提交，弹框，验证等的功能；以 AngularJS的思路，做声明式的架构设计时，我们通过指令和路由先设定好，什么样的操作干什么事情，等事件发生时，程序就会知道该干什么了。

今天说一下，AngularJS是如何实现前端路由功能的！

#### 目录

1. AngularJS路由介绍
2. 路由的代码实现
3. 实现效果截图

## 1. AngularJS路由介绍

AngularJS路由功能是一个纯前端的解决方案，与我们熟悉的后台路由不太一样。后台路由，通过不同的URL会路由到不同的控制器上(controller)，再渲染(render)到页面(HTML)。AngularJS的前端路由，需求提前对指定的(ng-app)，定义路由规则(routeProvider)，然后通过不同的URL，告诉(ng-app)加载哪个页面(HTML)，再渲染到(ng-app)视图(ng-view)中。

AngularJS的前端路由，虽然URL输入不一样，页面展示不一样，其实完成的单页(ng-app)视图(ng-view)的局部刷新。这样来看，AngularJS做单页应用就有点标配的感觉了。

从这个角度想想，要实现一个gmail的应用，真的就不难了。

## 2. 路由的代码实现

理论不多说了，直接上代码！！ 还是基于我们之前用[yeoman构建的项目](http://blog.fens.me/angularjs-yeoman-project/)。

业务场景：论坛功能，帖子列表页(list.html) 和 帖子内容页(detail.html)。

### 代码文件：

1. 增加：app/demo-route.html
2. 增加：app/views/route/list.html
3. 增加：app/views/route/detail.html
4. 修改: app/scripts/app.js
5. 修改: app/scripts/controllers/main.js

### 1). 增加：app/demo-route.html

这个文件是主页面(ng-app)，包含视图(ng-view)

```{bash}
<!doctype html>
<head>
<meta charset="utf-8">
<title>route</title>
</head>
<body ng-app="routeApp">
<h1>Route Demo index</h1>

<div ng-view></div>

<script src="bower_components/angular/angular.js"></script>
<script src="scripts/app.js"></script>
<script src="scripts/controllers/main.js"></script>
</body>
</html>
```

### 2). 增加：app/views/route/list.html

这个页面是布局模板，是HTML的代码片段。包括了一组ID的列表，通过ID列表的链接，可以进入到ID的详细页面。

```{bash}
<hr/>
<h3>Route : List.html</h3>

<ul>
<li ng-repeat="id in [1, 2, 3 ]">
<a href="#/list/{{ id }}"> ID{{ id }}</a>
</li>
</ul>
```

### 3). 增加：app/views/route/detail.html

这个页面是布局模板，是HTML的代码片段。通过ID访问，包含ID号, (ID的文章内容)

```{bash}
<hr/>
<h3>Route <span style="color: red;">{{id}}</span>: detail.html </h3>
```

### 4). 修改: app/scripts/app.js

这个是ng-app文件的定义，我们在demo-route.html中定义了routeApp，在这里需要声明。

```{bash}
var routeApp = angular.module('routeApp',[]);
routeApp.config(['$routeProvider',function ($routeProvider) {
      $routeProvider
      .when('/list', {
        templateUrl: 'views/route/list.html',
        controller: 'RouteListCtl'
      })
      .when('/list/:id', {
          templateUrl: 'views/route/detail.html',
          controller: 'RouteDetailCtl'
      })
      .otherwise({
        redirectTo: '/list'
      });
}]);
```

在routeApp模块中，我们定义了路由和布局模板。routeApp的默认URL是/list，即http://localhost:9000/demo-route.html#/list。 跳转详细页的路由是/list/:id，id为参数。

同时，/list的布局模板是views/route/list.html，属于RouteListCtl的控制器管理空间。

### 5). 修改: app/scripts/controllers/main.js

这个文件定义控制器controller。

```{bash}
routeApp.controller('RouteListCtl',function($scope) {
});
routeApp.controller('RouteDetailCtl',function($scope, $routeParams) {
    $scope.id = $routeParams.id;
});
```

分别对应该路由中的两个控制器声明。

程序写好，我们打开浏览器看效果。

## 3. 实现效果截图

别忘了用下面命令，启动程序。

```{bash}
grunt server
```

浏览器被自动打开，默认出的是http://localhost:9000/demo-route.html#/list, “#/list”是被redirectTo转向的结果。

![](http://blog.fens.me/wp-content/uploads/2013/08/AngularJS-route1.png)

点击ID2的链接。

![](http://blog.fens.me/wp-content/uploads/2013/08/AngularJS-route2.png)

页面被刷新了，出了detil的页面。同时，我们注意观察，页面没有整个刷新，而在视图中(ng-view)做的局部刷新。因为，chrome的开发工具的监控中，只是看到detail.html被加载。

我们再浏览地址栏中，输入212

```{bash}
http://localhost:9000/demo-route.html#/list/212
```

![](http://blog.fens.me/wp-content/uploads/2013/08/AngularJS-route3.png)

观察chrome的开发工具的监控中，没有任何的networking操作。

在浏览地址栏中，再输入原来list的地址

```{bash}
http://localhost:9000/demo-route.html#/list
```

观察chrome的开发工具的监控，确认没有任何变化！！

从这个实验，我们看到AngularJS纯前端路由的实现思路，配合视图的局部刷新，把业务功能切片后分散到HTML的模板页面中。非常容易地实现了widget。并且，这种widget可重用性会非常高，能大大减少前端代码量。

后端组件化开发思路，流畅的嵌入前端。爽死啦！！！

#### 转载请注明出处：http://blog.fens.me/angularjs-route-template/

