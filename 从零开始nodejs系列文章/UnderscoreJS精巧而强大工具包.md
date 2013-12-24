UnderscoreJS精巧而强大工具包
=========

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-underscore/

![UnderscoreJS精巧而强大工具包](http://blog.fens.me/wp-content/uploads/2013/08/underscore.png)


#### 前言

从其他语言转向Javascript时，通常都会遇到一些困惑性问题。比如，Java中的HashMap在Javascript中如何实现？Javascript面向对象式编程如何实现继承？如何实现通用的iterator对集合对象做遍历？如何对Array实现快速排序？….

如果你真的可以自己实现这些功能，那么你的Javascript基础很扎实的！我很佩服你！但对于大部分人来说，这些基础功能应该是由底层API支持的，就像JDK一样。Underscore为我们提供了这样的一个实用工具包，而且它真的很实用！

只有你动手做了，你才能有收获。

#### 目录

1. Underscore介绍
2. Underscore安装
3. 集合部分:数组或对象
4. 数组部分
5. 函数部分
6. 对象部分
7. 实用功能
8. 链式语法
9. 字符串处理Underscore.String

## 1. Underscore介绍

Underscore 是一个JavaScript实用库,提供了类似Prototype.js的一些功能,但是没有继承任何JavaScript内置对象。它弥补了部分jQuery没有实现的功能,同时又是Backbone.js必不可少的部分。

Underscore提供了80多个函数,包括常用的: map, select, invoke — 当然还有更多专业的辅助函数,如:函数绑定, JavaScript模板功能, 强类型相等测试, 等等. 在新的浏览器中, 有许多函数如果浏览器本身直接支持,将会采用原生的,如 forEach, map, reduce, filter, every, some 和 indexOf.

Underscore介绍，摘自 http://learning.github.io/underscore/

## 2. Underscore安装

Underscore.js是一个Javascript功能类库，不依赖于环境，可以加载到HTML中在浏览器运行，也可以直接在nodejs的环境中使用。

为了方便介绍函数功能，我这里选择在nodejs的环境中运行。

### 我的系统环境

```{bash}
win7 64bit, node v0.10.5, npm 1.2.19
```

### 安装Underscore

```{bash}
~ D:\workspace\javascript>mkdir nodejs-underscore
~ D:\workspace\javascript>cd nodejs-underscore

~ D:\workspace\javascript\nodejs-underscore>npm install underscore
npm http GET https://registry.npmjs.org/underscore
npm http 304 https://registry.npmjs.org/underscore
npm http GET https://registry.npmjs.org/underscore/-/underscore-1.5.1.tgz
npm http 200 https://registry.npmjs.org/underscore/-/underscore-1.5.1.tgz
underscore@1.5.1 node_modules\underscore
```

underscore有80多的函数，下面我只介绍一些，我比较感兴趣的，实用的。

## 3. 集合部分: 数组或对象

### 新建一个collection.js文件，测试underscore对集合的支持。

```{bash}
~ vi collection.js

//加载underscore库
var _ = require("underscore")._;
```

### each: 对集合循环操作

```{bash}
_.each([1, 2, 3], function (ele, idx) {
    console.log(idx + ":" + ele);
});
=> 0:1
1:2
2:3
```

### map: 对集合以map方式遍历，产生一个新数组

```{bash}
console.log(
    _.map([1, 2, 3], function(num){
        return num * 3;
    })
);
=> [3, 6, 9]
```

### reduce: 集合元素合并集的到memo

```{bash}
console.log(
    _.reduce([1, 2, 3], function (memo, num) {
        return memo + num;
    }, 0)
);
=> 6
```

### filter: 过滤集合中符合条件的元素。注：find:只返回第一个

```{bash}
console.log(
    _.filter([1, 2, 3, 4, 5, 6], function(num){
        return num % 2 == 0;
    })
);
=> [ 2, 4, 6 ]
```

### reject: 过滤集合中不符合条件的元素

```{bash}
console.log(
    _.reject([1, 2, 3, 4, 5, 6], function(num){
        return num % 2 == 0;
    })
);
=> [ 1, 3, 5 ]
```

### where: 遍历list, 返回新的对象数组

```{bash}
var list = [
    {title:"AAA",year: 1982},
    {title:"BBB",year: 1900},
    {title:"CCC",year: 1982}
];
console.log(
    _.where(list,{year: 1982})
);
=> [ { title: 'AAA', year: 1982 }, { title: 'CCC', year: 1982 } ]
```

### contains:判断元素是否在list中

```{bash}
console.log(
    _.contains([1, 2, 3], 3)
);
```

### invoke:通过函数名调用函数运行

```{bash}
console.log(
    _.invoke([[5, 1, 7]], 'sort')
);
=> [ [ 1, 5, 7 ] ]
```

### pluck: 提取一个集合里指定的属性值

```{bash}
var users = [
    {name: 'moe', age: 40},
    {name: 'larry', age: 50}
];
console.log(
    _.pluck(users, 'name')
);
=> [ 'moe', 'larry' ]
```

### max,min,sortBy: 取list中的最大,最小元素，自定义比较器

```{bash}
console.log(
    _.max(users, function (stooge) {
        return stooge.age;
    })
);
=> { name: 'larry', age: 50 }

var numbers = [10, 5, 100, 2, 1000];
console.log(
    _.min(numbers)
);
=> 2

console.log(
    _.sortBy([3, 4, 2, 1 , 6], function (num) {
        return Math.max(num);
    })
);
=> [ 1, 2, 3, 4, 6 ]
```

### groupBy: 把一个集合分组成多个集合

```{bash}
console.log(
    _.groupBy(['one', 'two', 'three'], 'length')
);
=> { '3': [ 'one', 'two' ], '5': [ 'three' ] }
```

### countBy: 把一个数据分组后计数

```{bash}
onsole.log(
    _.countBy([1, 2, 3, 4, 5], function (num) {
        return num % 2 == 0 ? 'even' : 'odd';
    })
);
=> { odd: 3, even: 2 }
```

### shuffle: 随机打乱一个数据

```{bash}
console.log(
    _.shuffle([1, 2, 3, 4, 5, 6])
);
=> [ 1, 5, 2, 3, 6, 4 ]
```

### toArray: 将list转换成一个数组

```{bash}
console.log(
    (function () {
        return _.toArray(arguments).slice(1);
    })(1, 2, 3, 4)
);
=> [ 2, 3, 4 ]
```

### size: 得到list中元素个数

```{bash}
console.log(
    _.size({one: 1, two: 2, three: 3})
);
```

## 4. 数组部分

### 新建一个array.js

```{bash}
~ vi array.js

var _ = require("underscore")._;
```

### first,last,initial,rest: 数组的元素操作。

```{bash}
var nums = [5, 4, 3, 2, 1];
console.log(_.first(nums));
console.log(_.last(nums));
console.log(_.initial(nums,1));
console.log(_.rest(nums,1));
=> 5
1
[ 5, 4, 3, 2 ]
[ 4, 3, 2, 1 ]
```

### indexOf,lastIndexOf,sortedIndex：取索引位置

```{bash}
console.log(_.indexOf([4, 2, 3, 4, 2], 4));
console.log(_.lastIndexOf([4, 2, 3, 4, 2], 4));
console.log(_.sortedIndex([10, 20, 30, 40, 50], 35));
=> 0
3
3
```

### range: 创建一个范围整数数组

```{bash}
console.log(_.range(1,10));
console.log(_.range(0, -10, -1));
=> [ 1, 2, 3, 4, 5, 6, 7, 8, 9 ]
[ 0, -1, -2, -3, -4, -5, -6, -7, -8, -9 ]
```

### compact:数组去除空值

```{bash}
console.log(
    _.compact([0, 1, false, 2, '', 3])
);
=> [ 1, 2, 3 ]
```

### flatten:将一个嵌套多层的数组(嵌套可以是任何层数)转换为只有一层的数组

```{bash}
console.log(
    _.flatten([1, [2], [3, [[4]]]])
);
=> [ 1, 2, 3, 4 ]
```

### without: 去掉元素

```{bash}
console.log(
    _.without([1, 2, 1, 0, 3, 1, 4], 0,1 )
);
=> [ 2, 3, 4 ]
```

### union,intersection,difference,uniq: 并集，交集，差集，取唯一

```{bash}
console.log(_.union([1, 2, 3], [101, 2, 1, 10], [2, 1]));
console.log(_.intersection([1, 2, 3], [101, 2, 1, 10], [2, 1]));
console.log(_.difference([1, 2, 3, 4, 5], [5, 2, 10]));
console.log(_.uniq([1, 2, 1, 3, 1, 2]));
=> [ 1, 2, 3, 101, 10 ]
[ 1, 2 ]
[ 1, 3, 4 ]
[ 1, 2, 3 ]
```

### zip: 合并多个数组中的元素，是group的反向操作

```{bash}
console.log(
    _.zip(['moe', 'larry', 'curly'], [30, 40, 50], [true, false, false])
);
=> [ [ 'moe', 30, true ],
  [ 'larry', 40, false ],
  [ 'curly', 50, false ] ]
```

### object: 把数组转换成对象

```{bash}
console.log(
    _.object(['moe', 'larry', 'curly'], [30, 40, 50])
);
=> { moe: 30, larry: 40, curly: 50 }
```

## 5. 函数部分

### 新建一个function.js

```{bash}
~ vi function.js
var _ = require("underscore")._;
```

### bind: 绑定函数到对象上, 无论何时函数被调用, 函数里的this都指向对象.

```{bash}
var func = function(greeting){ return greeting + ': ' + this.name };
func = _.bind(func, {name : 'moe'}, 'hi');
console.log(func());
=> hi: moe
```

### bindAll: 绑定方法名到对象上, 当这些方法被执行时将在对象的上下文执行. 绑定函数用作事件处理时非常方便, 否则函数调用时 this 关键字根本没什么用.

```{bash}
var buttonView = {
    label   : 'underscore',
    onClick : function(){ console.log('clicked: ' + this.label); },
    onHover : function(){ console.log('hovering: ' + this.label); }
};
var func  = _.bindAll(buttonView, 'onClick', 'onHover');
func.onClick();
=> clicked: underscore
```

### partial:在不改变this的情况下，通过参数填充数据

```{bash}
var add = function(a, b) { return a + b; };
add5 = _.partial(add, 5);
console.log(add5(10));
=> 15
```

### memoize: 通过缓存计算结果使函数具有记忆功能。

```{bash}
var fibonacci = _.memoize(function(n) {
    return n < 2 ? n : fibonacci(n - 1) + fibonacci(n - 2);
});
console.log(fibonacci(10));
=> 55
```

### delay: 在等待xx毫秒之后调用函数，类似于setTimeout

```{bash}
var log = _.bind(console.log, console);
_.delay(log, 1000, 'sleep 1s');
=> sleep 1s
```

### defer: 延迟调用函数, 直到当前调用栈被清空为止, 跟使用 setTimeout 赋予0毫秒的延时很像. 对执行高消耗算法或大型HTML呈现而不阻碍UI更新线程很有用.

```{bash}
_.defer(function(){ console.log('deferred'); });
=> deferred
```

### throttle:返回一个类似于节流阀一样的函数, 当高频率的调用函数, 实际上会每隔 wait 毫秒才会调用一次. 对于高到您感觉不到的高频率执行的函数时非常有用.

```{bash}
var throttled = _.throttle(function(){
    _(5).times(function(n){ console.log(n+":"+new Date()); });
}, 100);
throttled();
=> 0:Wed Aug 28 2013 14:20:48 GMT+0800
1:Wed Aug 28 2013 14:20:48 GMT+0800
2:Wed Aug 28 2013 14:20:48 GMT+0800
3:Wed Aug 28 2013 14:20:48 GMT+0800
4:Wed Aug 28 2013 14:20:48 GMT+0800
```

### debounce: 返回函数的防反跳版本, 将延迟函数的执行(真正的执行)在函数最后一次调用时刻的等待xx毫秒之后，可以实现延迟加载。

```{bash}
var lazyLoad = _.debounce(function(){
    console.log("lazy load 3s");
}, 3000);
lazyLoad();
=> lazy load 3s
```

### once: 创建一个只能运行一次的函数. 重复调用此修改过的函数会没有效果, 只会返回第一次执行时返回的结果。单例模式。

```{bash}
var initialize = _.once(function(){console.log('initialize');});
initialize();
initialize();
=> initialize
```

### after: 对循环计数，只有超过计数，才会调用指定的函数

```{bash}
var nums = [1,2,3,4];
var renderNums = _.after(nums.length, function(){
    console.log('render nums');
});
_.each(nums, function(num) {
    console.log('each:'+num);
    renderNums();
});
=> each:1
each:2
each:3
each:4
render nums
```

### wrap: 以函数作为函数传递，可以增加函数调用前后的控制。有点类似于 “模板方法模式”

```{bash}
var hello = function(name) { return "hello: " + name; };
hello = _.wrap(hello, function(func) {
    return "before, " + func("moe") + ", after";
});
console.log(hello());
=> before, hello: moe, after
```

### compose: 组合函数调用关系，把单独的f(),g(),h()组合成f(g(h()))

```{bash}
var greet    = function(name){ return "A: " + name; };
var exclaim  = function(statement){ return "B: "+statement + "!"; };
var welcome = _.compose(exclaim, greet);
console.log(welcome('moe'));
=> B: A: moe!
```

## 6. 对象部分

### 新建一个object.js

```{bash}
~ vi object.js
var _ = require("underscore")._;
```

### keys,values,paris,invert: 取属性名，取属性值，把对象转换成[key,value]数组，对调键值

```{bash}
var obj = {one: 1, two: 2, three: 3}
console.log(_.keys(obj));
console.log(_.values(obj));
console.log(_.pairs(obj));
console.log(_.invert(obj));
=> [ 'one', 'two', 'three' ]
[ 1, 2, 3 ]
[ [ 'one', 1 ], [ 'two', 2 ], [ 'three', 3 ] ]
{ '1': 'one', '2': 'two', '3': 'three' }
```

### functions：返回对象的所有方法名

```{bash}
var fun = {
    fun1:function(){},
    fun2:function(){}
}
console.log(_.functions(fun));
=> [ 'fun1', 'fun2' ]
```

### extend: 复制对象的所有属性到目标对象上，覆盖已有属性

```{bash}
console.log(
    _.extend({name : 'moe'}, {age : 50})
);
=> { name: 'moe', age: 50 }
```

### defaults: 复制对象的所有属性到目标对象上，跳过已有属性

```{bash}
var iceCream = {flavor : "chocolate"};
console.log(
    _.defaults(iceCream, {flavor : "vanilla", sprinkles : "lots"})
);
=> { flavor: 'chocolate', sprinkles: 'lots' }
```

### pick,omit: 返回一个对象的副本，保留指定的属性或去掉指定的属性

```{bash}
console.log(
    _.pick({name : 'moe', age: 50, userid : 'moe1'}, 'name', 'age')
);
=> { name: 'moe', age: 50 }
console.log(
    _.omit({name : 'moe', age : 50, userid : 'moe1'}, 'userid')
);
=> { name: 'moe', age: 50 }
```

### clone: 引入方式克隆对象，不进行复制

```{bash}
console.log(
   _.clone({name : 'moe'});
);
=> {name : 'moe'};
```

### tag: 用对象作为参数来调用函数，作为函数链式调用的一环

```{bash}
console.log(
    _.chain([1,2,3,200])
        .filter(function(num) { return num % 2 == 0; })
        .tap(console.log)
        .map(function(num) { return num * num })
        .value()
);
=> [ 2, 200 ]
[ 4, 40000 ]
```

### has: 判断对象是否包含指定的属性名

```{bash}
console.log(_.has({a: 1, b: 2, c: 3}, "b"));
```

### isEqual: 判断两个对象是值相等

```{bash}
var moe   = {name : 'moe', luckyNumbers : [13, 27, 34]};
var clone = {name : 'moe', luckyNumbers : [13, 27, 34]};
console.log(moe == clone);
=> false
console.log(_.isEqual(moe, clone));
=> true
```

### 判断对象类型的方法，下面反回值都是true

```{bash}
console.log(_.isEmpty({}));
console.log(_.isArray([1,2,3]));
console.log(_.isObject({}));
console.log((function(){ return _.isArguments(arguments); })(1, 2, 3));
console.log(_.isFunction(console.log));
console.log(_.isString("moe"));
console.log(_.isNumber(8.4 * 5));
console.log(_.isFinite(-101));
console.log(_.isBoolean(true)); 
console.log(_.isDate(new Date()));
console.log(_.isNaN(NaN));
console.log(_.isNull(null));
console.log(_.isUndefined(undefined));
=> true
```

## 7. 实用功能

### 新建一个util.js

```{bash}
~ vi util.js
var _ = require("underscore")._;
```

### noConflict: 把 “_” 变量的控制权预留给它原有的所有者. 返回一个引用给 Underscore 对象.

```{bash}
var underscore = _.noConflict();
```

### identity: 返回与传入参数相等的值. 相当于数学里的: f(x) = x

```{bash}
var moe = {name : 'moe'};
console.log(moe === _.identity(moe));
=> true
```

### times: 设计调用次数

```{bash}
_(3).times(function(n){ console.log(n); });
=> 0
1
2
```

### random: 返回范围内的随机数

```{bash}
console.log(_.random(0, 100));
=> 30
```

### mixin: 封装自己的函数到Underscore对象中，后面Underscore.string就是这种方式的集成

```{bash}
_.mixin({
    capitalize : function(string) {
        return string.charAt(0).toUpperCase() + string.substring(1).toLowerCase();
    }
});
console.log(_("fabio").capitalize());
=> Fabio
```

### uniqueId:产生一个全局的唯一id，以参数作为前缀

```{bash}
console.log(_.uniqueId('contact_'));
=> contact_1
console.log(_.uniqueId('contact_'));
=> contact_2
```

### escape,unescape:转义HTML字符串，反转到HTML字符串

```{bash}
console.log(_.escape('Curly, Larry & Moe'));
=> Curly, Larry &amp; Moe
console.log(_.unescape('Curly, Larry &amp; Moe'));
=> Curly, Larry & Moe
```

### result: 通过字符串调用对象的函数，或返回属性值

```{bash}
var obj = {cheese: 'crumpets', stuff: function(){ return 'nonsense'; }};
console.log(_.result(obj, 'cheese'));
=> crumpets
console.log(_.result(obj, 'stuff'));
=> nonsense
```

### template: 将 JavaScript 模板编译为可以用于页面呈现的函数, 对于通过JSON数据源生成复杂的HTML并呈现出来的操作非常有用. 模板函数可以通过以下两种方式插入到页面中, 使用<%= … %>, 也可以用<% … %>执行任意的 JavaScript 代码. 如果您希望插入一个值, 并让其进行HTML转义, 当您使用创建一个模板时使用 <%- … %> , 传入一个含有与模板对应属性的对象 data. 如果您要写一个一次性的, 您可以传对象 data 作为第二个参数给模板template 来直接呈现, 这样页面会立即呈现而不是返回一个模板函数. 参数 settings 是一个哈希表包含任何可以覆盖的设置 _.templateSettings.

```{bash}
var compiled = _.template("hello: <%= name %>");
console.log(compiled({name : 'moe'}));
=>hello: moe

var list = "<% _.each(people, function(name) { %> <li><%= name %></li> <% }); %>";
console.log(_.template(list, {people : ['moe', 'curly', 'larry']}));
=> <li>moe</li> <li>curly</li> <li>larry</li>

var template = _.template("<b><%- value %></b>");
console.log(template({value : '<script>'}));
=> <b>&lt;script&gt;</b>

var compiled = _.template("<% print('Hello ' + epithet); %>");
console.log(compiled({epithet: "stooge"}));
=>Hello stooge

console.log(_.template("Using 'with': <%= data.answer %>", {answer: 'no'}, {variable: 'data'}));
=>Using 'with': no

_.templateSettings = {
interpolate : /\{\{(.+?)\}\}/g
};
var template = _.template("Hello {{ name }}!");
console.log(template({name : "Mustache"}));
=>Hello Mustache!
```

## 8. 链式语法

### 新建一个chaining.js

```{bash}
~ vi chaining.js
var _ = require("underscore")._;
```

### chain: 返回一个封装的对象. 在封装的对象上调用方法会返回封装的对象本身, 直到value() 方法调用为止.

```{bash}
var stooges = [{name : 'curly', age : 25}, {name : 'moe', age : 21}, {name : 'larry', age : 23}];
var youngest = _.chain(stooges)
    .sortBy(function(stooge){ return stooge.age; })
    .map(function(stooge){ return stooge.name + ' is ' + stooge.age; })
    .first()
    .value();
console.log(youngest);
=> moe is 21
```

### 对一个对象使用 chain 方法, 会把这个对象封装并 让以后每次方法的调用结束后都返回这个封装的对象, 当您完成了计算, 可以使用 value 函数来取得最终的值. 以下是一个同时使用了 map/flatten/reduce 的链式语法例子, 目的是计算一首歌的歌词里每一个单词出现的次数.

```{bash}
var lyrics = [
    {line : 1, words : "I'm a lumberjack and I'm okay"},
    {line : 2, words : "I sleep all night and I work all day"},
    {line : 3, words : "He's a lumberjack and he's okay"},
    {line : 4, words : "He sleeps all night and he works all day"}
];
console.log(
    _.chain(lyrics)
        .map(function(line) { return line.words.split(' '); })
        .flatten()
        .reduce(function(counts, word) {
            counts[word] = (counts[word] || 0) + 1;
            return counts;
        }, {})
        .value()
);
=> { 'I\'m': 2,
  a: 2,
  lumberjack: 2,
  and: 4,
  okay: 2,
  I: 2,
  sleep: 1,
  all: 4,
  night: 2,
  work: 1,
  day: 2,
  'He\'s': 1,
  'he\'s': 1,
  He: 1,
  sleeps: 1,
  he: 1,
  works: 1 }
```
### value: 提取封装对象的最终值，作为chain()结束标志。

```{bash}
console.log(_([1, 2, 3]).value());
```

## 9. 字符串处理Underscore.String

### 安装underscore.string

```{bash}
~ D:\workspace\javascript\nodejs-underscore>npm install underscore.string
npm http GET https://registry.npmjs.org/underscore.string
npm http 304 https://registry.npmjs.org/underscore.string
underscore.string@2.3.3 node_modules\underscore.string
```

### 新建一个string.js，通过mixin()函数,让underscore.string和underscore集成统计实现_.fun()语法。

```{bash}
~ vi string.js

var _  = require('underscore');
_.str = require('underscore.string');
_.mixin(_.str.exports());
```

### 字符串的数字格式化

```{bash}
console.log(_.numberFormat(1000, 2));
=> 1,000.00
console.log(_.numberFormat(123456789.123, 5, '.', ','));
=> 123,456,789.12300
console.log(_('2.556').toNumber());
=> 3
console.log(_('2.556').toNumber(2));
=> 2.56
console.log(_.sprintf("%.1f", 1.17));
=> 1.2
```

### 字符串基础操作

```{bash}
console.log(_.levenshtein('kitten', 'kittah'));
=> 2
console.log(_.capitalize('epeli'));
=> Epeli
console.log(_.chop('whitespace', 3));
=> [ 'whi', 'tes', 'pac', 'e' ]
console.log(_.clean(" foo    bar   "));
=> foo bar
console.log(_.chars('Hello'));
=> [ 'H', 'e', 'l', 'l', 'o' ]
console.log(_.swapCase('hELLO'));
=> Hello
console.log(_.str.include("foobar", "ob")); //不兼容API，需要用_.str.fun()
=> true
console.log(_.str.reverse("foobar"));//不兼容API，需要用_.str.fun()
=> raboof
console.log(_('Hello world').count('l'));
=> 3
console.log(_('Hello ').insert(6, 'world'));
=> Hello world
console.log(_('').isBlank() && _('\n').isBlank() && _(' ').isBlank());
=> true
console.log(_.join(",", "foo", "bar"));
=> foo,bar
console.log(_.lines("Hello\nWorld"));
=> [ 'Hello', 'World' ]
console.log(_("image.gif").startsWith("image"));
=> true
console.log(_("image.gif").endsWith("gif"));
=> true
console.log(_('a').succ());//指下编码的下一个
=> b
```

### 字符串变换

```{bash}
console.log(_.repeat("foo", 3));
=> foofoofoo
console.log(_.repeat("foo", 3, "bar"));
=> foobarfoobarfoo
console.log(_.surround("foo", "ab"));
=> abfooab
console.log(_.quote('foo', "#"));
=> #foo#
console.log(_.unquote('"foo"'));
=> foo
console.log(_.unquote("'foo'", "'"));
=> foo
console.log(_.slugify("Un éléphant à l'orée du bois"));
=> un-elephant-a-loree-du-bois
console.log(['foo20', 'foo5'].sort(_.naturalCmp));
=> [ 'foo5', 'foo20' ]
console.log(_.toBoolean("true"));
=> true
console.log(_.toBoolean("truthy", ["truthy"], ["falsy"]));
=> true
console.log(_.toBoolean("true only at start", [/^true/]));
=> true
```

### 字符串替换,截断

```{bash}
console.log(_('https://edtsech@bitbucket.org/edtsech/underscore.strings').splice(30, 7, 'epeli'));
=> https://edtsech@bitbucket.org/epeli/underscore.strings
console.log(_.trim("  foobar   "));
=> foobar
console.log(_.trim("_-foobar-_", "_-"));
=> foobar
console.log(_('Hello world').truncate(5));
=> Hello...
console.log(_('Hello, world').prune(5));
=> Hello...
console.log(_('Hello, world').prune(5, ' (read a lot more)'));
=> Hello, world
console.log(_.words("   I   love   you   "));
=> [ 'I', 'love', 'you' ]
console.log(_.words("I-love-you", /-/));
=> [ 'I', 'love', 'you' ]
console.log(_('This_is_a_test_string').strRight('_'));
=> is_a_test_string
console.log(_('This_is_a_test_string').strRightBack('_'));
=> string
console.log(_('This_is_a_test_string').strLeft('_'));
=> This
console.log(_('This_is_a_test_string').strLeftBack('_'));
=> This_is_a_test
```

### 字符串占位

```{bash}
console.log(_.pad("1", 8));
=>        1
console.log(_.pad("1", 8, '0'));
=> 00000001
console.log(_.pad("1", 8, '0', 'right'));
=> 10000000
console.log(_.pad("1", 8, 'bleepblorp', 'both'));
=> bbbb1bbb
console.log(_.lpad("1", 8, '0'));
=> 00000001
console.log(_.rpad("1", 8, '0'));
=> 10000000
console.log(_.lrpad("1", 8, '0'));
=> 00001000
```

### 字符串语义处理

```{bash}
console.log(_.toSentence(['jQuery', 'Mootools', 'Prototype']));
=> jQuery, Mootools and Prototype
console.log(_.toSentence(['jQuery', 'Mootools', 'Prototype'], ', ', ' unt '));
=> jQuery, Mootools unt Prototype
console.log(_.toSentenceSerial(['jQuery', 'Mootools']));
=> jQuery and Mootools
console.log(_.toSentenceSerial(['jQuery', 'Mootools', 'Prototype']));
=> jQuery, Mootools, and Prototype
console.log(_('my name is epeli').titleize());
=> My Name Is Epeli
console.log(_('-moz-transform').camelize());
=> MozTransform
console.log(_('some_class_name').classify());
=> SomeClassName
console.log(_('MozTransform').underscored());
=> moz_transform
console.log(_('MozTransform').dasherize());
=> -moz-transform
console.log(_('  capitalize dash-CamelCase_underscore trim  ').humanize());
=> Capitalize dash camel case underscore trim
```

### HTML相关操作

```{bash}
console.log(_('<div>Blah blah blah</div>').escapeHTML());
=> &lt;div&gt;Blah blah blah&lt;/div&gt;
console.log(_('&lt;div&gt;Blah blah blah&lt;/div&gt;').unescapeHTML());
=><div>Blah blah blah</div>
console.log(_('a <a href="#">link</a>').stripTags());
=>a link
console.log(_('a <a href="#">link</a><script>alert("hello world!")</script>').stripTags());
=>a linkalert("hello world!")
```

## 10. 写在最后的话

写在最后的话，本来只是想列举一些函数的，做了3遍真是无从取舍，把API就直接搬过来了。Javascript有如此精巧而强大的类库，顿时让我感到Java的大而臃肿。每种语言的发展历程，确实让我看到了从底到顶的不一样，同时也造就了从顶到底也不一样。不管是代码风格，设计模式，思维模式，架构设计等等，所以掌握多门语言是非常重要的，各取其精华相互借鉴，最后才能让我们树立自己的想法，做出开山立派之大事。

文中实例大部分来自官方：我只做了一些小调整。

underscore 1.5.1: http://underscorejs.org/

underscore中文 1.4.2: http://learning.github.io/underscore

underscore.string: https://github.com/epeli/underscore.string

#### 转载请注明出处：http://blog.fens.me/nodejs-underscore/

