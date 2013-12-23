jasmine行为驱动,测试先行
=========

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-jasmine-bdd

![jasmine行为驱动,测试先行](http://blog.fens.me/wp-content/uploads/2013/08/jasmine1.png)

#### 前言
TDD(Test Driven Development)测试驱动开发，是敏捷开发中提出的最佳实践之一。jasmine很有意思的提出了BDD(Behavior Driven Development)行为驱动开发，诱发了我的好奇心，一探究竟。

测试驱动开发，对软件质量起到了规范性的控制。未写实现，先写测试，一度成为Java领域研发的圣经。随着Javascript兴起，功能越来越多，代码量越来越大，开发人员素质相差悬殊，真的有必要建立对代码的规范性控制。jasmine就是为团队合作而生。

#### 目录

1. jasmine介绍
2. jasmine安装
3. jasmine环境配置
4. jasmine使用

## 1. jasmine介绍

Jasmine是一个用来编写Javascript测试的框架，它不依赖于任何其它的javascript框架，也不需要对DOM。它有拥有灵巧而明确的语法可以让你轻松的编写测试代码。

jasmine的结构很简单：

```{bash}

describe("A suite", function() {
  var foo;
  beforeEach(function() {
    foo = 0;
    foo += 1;
  });

  afterEach(function() {
    foo = 0;
  });

  it("contains spec with an expectation", function() {
    expect(true).toBe(true);
  });
});
```

每个测试都在一个测试集中运行，Suite就是一个测试集，用describe函数封装。 Spec表示每个测试用例，用it函数封装。通过expect函数，作为程序断言来判断相等关系。setup过程用beforeEach函数封装，tearDown过程用afterEach封装。

用过Java中JUnit的同学，看到这种对应关系，应该就会明白jasmine就是JUnit的Javascript重写版。

## 2. jasmine安装

安装jasmine类库时，让我们利用bower，一键搞定。bower的使用介绍，请参考文章：[bower解决js的依赖管理](http://blog.fens.me/nodejs-bower-intro/)。把已经学到的知识用起来，效率马上提升10倍。

```{bash}

~ D:\workspace\javascript>mkdir jasmine
~ D:\workspace\javascript>cd jasmine

#查找jasmine项目
~ D:\workspace\javascript\jasmine>bower search jasmine
-----------------------------------------
Update available: 1.2.4 (current: 1.1.2)
Run npm update -g bower to update
-----------------------------------------

Search results:

    jasmine git://github.com/pivotal/jasmine.git
    jasmine-jquery git://github.com/velesin/jasmine-jquery
    jasmine-sinon git://github.com/froots/jasmine-sinon.git
    jasmine-ajax git://github.com/pivotal/jasmine-ajax.git
    jasmine.async git://github.com/derickbailey/jasmine.async.git
    flight-jasmine git://github.com/twitter/flight-jasmine.git
    jasmine-jstd-adapter git://github.com/ibolmo/jasmine-jstd-adapter.git
    jasmine-flight git://github.com/flightjs/jasmine-flight.git
    jasmine-signals git://github.com/AdamNowotny/jasmine-signals.git
    jasmine-underscore git://github.com/adscott/jasmine-underscore.git
    jasmine-bootstrap git://github.com/esbie/jasmine-bootstrap.git
    jasmine-data-provider git://github.com/sublimino/jasmine-data-provider.git
    jasmine-sugar git://github.com/fantactuka/jasmine-sugar.git

#安装jasmine类库
~ D:\workspace\javascript\jasmine>bower install jasmine
bower jasmine#*             not-cached git://github.com/pivotal/jasmine.git#*
bower jasmine#*                resolve git://github.com/pivotal/jasmine.git#*
bower jasmine#*               download https://github.com/pivotal/jasmine/archive/v1.3.1.tar.gz
bower jasmine#*                extract archive.tar.gz
bower jasmine#*               resolved git://github.com/pivotal/jasmine.git#1.3.1
bower jasmine#~1.3.1           install jasmine#1.3.1

jasmine#1.3.1 bower_components\jasmine
```

## 3. jasmine环境配置

### jasmine运行需要4个部分：

1. 运行时环境：我们这里基于chrome浏览器，通过HTML作为javascript载体
2. 源文件：用于实现某种业务逻辑的文件，就是我们平时写的js脚本
3. 测试文件：符合jasmineAPI的测试js脚本
4. 输出结果：jasmine提供了基于网页的输出结果

下面让我开始jasmine：

#### 1). 新建一个HTML文件：test.html

```{bash}
~ vi test.html

<!DOCTYPE html>
<html>
<head>
<title>jasmine test</title>
<link rel="stylesheet" type="text/css" href="bower_components/jasmine/lib/jasmine-core/jasmine.css">
<script type="text/javascript" src="bower_components/jasmine/lib/jasmine-core/jasmine.js"></script>
<script type="text/javascript" src="bower_components/jasmine/lib/jasmine-core/jasmine-html.js"></script>
</head>
<body>
<h1>jasmine test</h1>
<script type="text/javascript" src="src.js"></script>
<script type="text/javascript" src="test.js"></script>
<script type="text/javascript" src="report.js"></script>
</body>
</html>
```

我们看到页面上面有5个javascript的导入：

+ jasmine.js：核心文件用于执行单元测试的类库
+ jasmine-html.js:用于显示单元测试的结果的类库
+ src.js:我们自己的业务逻辑的js
+ test.js:单元测试的js
+ report.js:用于启动单元测试js

jasmine.js,jasmine-html.js是系统类库，直接引入。

report.js，是启动脚本，写法是固定的。

```{bash}

~ vi report.js

(function() {
    var jasmineEnv = jasmine.getEnv();
    jasmineEnv.updateInterval = 1000;

    var htmlReporter = new jasmine.HtmlReporter();

    jasmineEnv.addReporter(htmlReporter);

    jasmineEnv.specFilter = function(spec) {
        return htmlReporter.specFilter(spec);
    };

    var currentWindowOnload = window.onload;

    window.onload = function() {
        if (currentWindowOnload) {
            currentWindowOnload();
        }
        execJasmine();
    };

    function execJasmine() {
        jasmineEnv.execute();
    }

})();
```

src.js是实现业务逻辑的文件，我们定义sayHello的函数

```{bash}
~ vi src.js

function sayHello(name){
    return "Hello " + name;
}
```

test.js对源文件进行单元测试

```{bash}
~ vi test.js

describe("A suite of basic functions", function() {
    var name;

    it("sayHello", function() {
        name = "Conan";
        var exp = "Hello Conan";
        expect(exp).toEqual(sayHello(name));
    });
});

```


页面报告截图：

![](http://blog.fens.me/wp-content/uploads/2013/08/test1.png)

我们完成了，最基础的jasmine功能。

## 4. jasmine使用

### 1). 测试先行

测试先行，就是未写现实，先写用例。

我们刚才已经配置好了jasmine的环境，后面我们所有功能代码都写在src.js中，测试代码都写在test.js中。

有一个需求，要实现单词倒写的功能。如：”ABCD” ==> “DCBA”

我们编辑test.js，增加一个测试用例

```{bash}

~ vi test.js

it("reverse word",function(){
    expect("DCBA").toEqual(reverse("ABCD"));
});
```

执行这个页面，单元测试失败，提示没有reverse函数。

![](http://blog.fens.me/wp-content/uploads/2013/08/test2.png)

编辑src.js，增加reverse函数

```{bash}
function reverse(name){
    return "DCBA";
}
```


执行页面，单元测试成功！

![](http://blog.fens.me/wp-content/uploads/2013/08/test3.png)

单元测试成功，是不是能说明我们完成了“单词倒写”的功能呢？答案是不确定的。如果想保证功能是正确性，我们需要进行更多次的验证。

编辑test.js，继续增加测试用例

```{bash}
    it("reverse word",function(){
        expect("DCBA").toEqual(reverse("ABCD"));
        expect("Conan").toEqual(reverse("nanoC"));
    });
```

刷新页面，又提示单元测试失败，因为我们希望输入是”Conan”，输出是”nanoC”，但是功能代码返回是”DCBA”，显然业务逻辑写的是不对的。

修改src.js，修改reverse函数

```{bash}

~ vi src.js

function reverse(name){
    return name.split("").reverse().join("");
}
```

再次刷新页面，单元测试成功！！

这是敏捷开发中提倡的“测试先行”的案例，对于产品级的代码，我们真的应该要高质量控制。

### 2). jasmine语法实践

以下内容是对jasmine语法的介绍，都在test.js中实现。

做一个嵌套的describe(“A suite of jasmine’s function”)

#### 对断言表达式的使用

```{bash}

describe("A suite of jasmine's function", function() {
    describe("Expectations",function(){
        it("Expectations",function(){
            expect("AAA").toEqual("AAA");
            expect(52.78).toMatch(/\d*.\d\d/);
            expect(null).toBeNull();
            expect("ABCD").toContain("B");
            expect(52,78).toBeLessThan(99);
            expect(52.78).toBeGreaterThan(18);

            var x = true;
            var y;
            expect(x).toBe(true);
            expect(x).toBeDefined();
            expect(y).toBeUndefined();
            expect(x).toBeTruthy();
            expect(!x).toBeFalsy();

            var fun = function() { return a + 1;};
            expect(fun).toThrow();
        });
    });
});
```

#### 对开始前和使用后的变量赋值

```{bash}
    describe("Setup and Teardown",function(){
        var foo;
        beforeEach(function() {
            foo = 0;
            foo += 1;
        });
        afterEach(function() {
            foo = 0;
        });

        it("is just a function, so it can contain any code", function() {
            expect(foo).toEqual(1);
        });

        it("can have more than one expectation", function() {
            expect(foo).toEqual(1);
            expect(true).toEqual(true);
        });
    });
```

#### 对忽略suite的声明

```{bash}
   xdescribe("Disabling Specs and Suites", function() {
        it("Disabling Specs and Suites",function(){
            expect("AAA").toEqual("AAA");
        });
    });
```

#### 对异步程序的单元测试

```{bash}
   describe("Asynchronous Support",function(){
        var value, flag;
        it("Asynchronous Support", function() {
            runs(function() {
                flag = false;
                value = 0;
                setTimeout(function() {
                    flag = true;
                }, 500);
            });
            waitsFor(function() {
                value++;
                return flag;
            }, "The Value should be incremented", 750);

            runs(function() {
                expect(value).toBeGreaterThan(0);
            });
        });
    });
```

我们成功地对Javascript完成各种的单元测试，下面是测试报告。

![](http://blog.fens.me/wp-content/uploads/2013/08/test4.png)

最后，BDD其实就是TDD。所以，不要被新名词吓到，实质是一样的。

#### 转载请注明出处：http://blog.fens.me/nodejs-jasmine-bdd

