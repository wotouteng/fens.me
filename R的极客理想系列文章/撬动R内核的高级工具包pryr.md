撬动R内核的高级工具包pryr
===========

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-pryr/

![撬动R内核的高级工具包pryr](http://blog.fens.me/wp-content/uploads/2014/04/r-pryr.png)

#### 前言

随着对R语言的使用越来越深入，我们需要更多的对R语言底层的进行了解，比如数据结构S3,S4对象，函数的调用机制等。pryr包就是可以帮助我们了解R语言运行机制的工具。利用pryr包，我们可以更容易地接触R的核心。

本文为R语言的高级内容。

#### 目录

1. pryr介绍
2. pryr安装
3. pryr使用

## 1 pryr介绍

pryr包是一个深层的了解R语言运行机制的工具，可以帮助我们更加贴近R语言的核心。为了能开发出更高级R语言应用，需要我们更深入地懂R。

### pryr的API介绍

内部实现工具：

+ promise对象：uneval(), is_promise()
+ 查询环境变量： where(), rls(), parenv()
+ 查看闭包函数变量： unenclose()
+ 函数调用关系：call_tree()
+ 查看对象底层对应的C语言类型 address(), refs(), typename()
+ 跟踪对象是否被修改track_copy()

面向对象检查：

+ 判断属于哪种类型对象： otype()
+ 判断属于哪种类型函数： ftype()

辅助编程函数：

+ 通过参数创建函数：make_function(), f()
+ 变量表达式替换：substitute_q(), subs()
+ 批量修改对象： modify_lang()
+ 快速创建list对象：dots(), named_dots()
+ 建匿名函数调用：partial()
+ 找符合条件函数：find_funs()

代码简化工具：

+ 创建延迟或直接绑定：%<d-%, %<a-%
+ 创建常量绑定：%<c-%
+ 重新绑定：rebind, <<-

## 2 pryr安装

本文使用的系统环境

+ Linux Ubuntu 12.04.2 LTS 64bit
+ R 3.0.1 x86_64-pc-linux-gnu (64-bit)

由于项目pryr，还没有发布到CRAN，仅支持从github安装。我们要使用devtools包来通过github来安装。关于devtools包的使用，请参考文章：[在巨人的肩膀前行 催化R包开发](http://blog.fens.me/r-package-faster/)

pryr安装

```{bash}
~ R

# 安装devtools
# install.packages("devtools")

> library(devtools)
> install_github("pryr")
```

> 注：我尝试在Win7下安装，但出现了编译错误。

## 3 pryr使用

+ 3.1 创建匿名函数f()
+ 3.2 通过参数创建函数make_function()
+ 3.3 创建匿名函数调用partial()
+ 3.4 变量表达式替换substitute_q(), subs()
+ 3.5 面向对象类型判断otype(),ftype()
+ 3.6 查看对象底层的C语言类型 address(), refs(), typename()
+ 3.7 查看对象是否被修改track_copy()
+ 3.8 查看闭包函数变量 unenclose()
+ 3.9 批量修改对象 modify_lang()
+ 3.10 快速创建list对象 dots(), named_dots()
+ 3.11 查找符合条件函数 fun_calls()
+ 3.12 查询环境变量 where(), rls(), parenv()
+ 3.13 打印函数调用关系 call_tree(), ast()
+ 3.14 promise对象 uneval(), is_promise()
+ 3.15 数据绑定%<a-%, %<c-%，%<d-%, rebind,<<-

### 3.1 创建匿名函数f()

通过使用f()函数，可以实现创建匿名函数，在单行完成函数定义、调用、运算的操作。

```{bash}
# 创建一个匿名函数
> f(x + y)
function (x, y)
x + y

# 创建一个匿名函数，并赋值计算
> f(x + y)(1, 10)
[1] 11

# 创建一个匿名函数，指定参数和默认值
> f(x, y = 2, x + y)
function (x, y = 2)
x + y

# 创建一个匿名函数，并赋值计算
> f(x, y = 2, x + y)(1)
[1] 3

# 创建一个匿名函数，多行运行，并赋值计算
>  f({y <- runif(1); x + y})(3)
[1] 3.7483
```

### 3.2 通过参数创建函数make_function()

通过使用make_function()函数，可以通过make_function()函数的3个参数，来创建一个普通的函数，从而现实动态性。

make_function()函数的3个参数分别是：

+ 生成函数的参数部分, list类型
+ 生成函数的表达式部分, 语法表达式, call类型
+ 生成函数的系统环境部分, environment类型

```{bash}
# 创建标准的函数
> f <- function(x) x + 3
> f
function(x) x + 3

# 运行函数
> f(12)
[1] 15

# 通过参数创建函数
> g <- make_function(alist(x = ), quote(x + 3))
> g
function (x)
x + 3

# 运行函数
> g(12)
[1] 15
```

### 3.3 创建匿名函数调用partial()

使用partial()函数，可以减少参数定义的过程，方便匿名函数的调用

```{bash}
# 定义一个普通的函数
> compact1 <- function(x) Filter(Negate(is.null), x)
> compact1
function(x) Filter(Negate(is.null), x)

# 通过partial定义的匿名函数
> compact2 <- partial(Filter, Negate(is.null))
> compact2
function (...)
Filter(Negate(is.null), ...)
```

我们看到，上面的两个函数定义很像，一个是有明确的参数定义，另一个用partial()则是隐式的参数定义。

再看另一例：输出runif()均匀分布的结果

```{bash}
# 标准函数实现
> f1 <- function(){runif(rpois(1, 5))}
> f1()
[1] 0.09654228 0.93089395 0.85530142 0.33021067 0.16728877 0.79099825
> f1()
[1] 0.6166580 0.2100876 0.3125176

# 通过partial的匿名函数调用
> f2 <- partial(runif, n = rpois(1, 5))
> f2()
[1] 0.25955143 0.12858459 0.04994997 0.11505708 0.10509429
> f2()
[1] 0.9710866 0.1469317
```

### 3.4 变量表达式替换 substitute_q(), subs()

使用substitute_q()函数，可以对表达式调用，直接进行参数替换

```{bash}
# 定义一个表达式调用
>  x <- quote(a + b)
> class(x)
[1] "call"

# 对x调用参数替换，无效
>  substitute(x, list(a = 1, b = 2))
x

# 对直接变量参数替换
> substitute(a+b, list(a = 1, b = 2))
1 + 2

# 对x调用参数替换
>  substitute_q(x, list(a = 1, b = 2))
1 + 2

执行参数调用
> eval(substitute_q(x, list(a = 1, b = 2)))
[1] 3
```

使用subs()函数，可以直接对变量表达式替换

```{bash}
> a <- 1
> b <- 2

# 对变量表达式替换，无效
> substitute(a + b)
a + b

# 对变量表达式替换
> subs(a + b)
1 + 2
```

### 3.5 面向对象类型判断otype(),ftype()

判断对象类型：通过otype()函数可以很容易的分辨出基本类型，S3类型，S4类型，RC类型的对象，比起内置的类型检查要高效的多。

```{bash}
# 基本类型
> otype(1:10)
[1] "primitive"
> otype(c('a','d'))
[1] "primitive"
> otype(list(c('a'),data.frame()))
[1] "primitive"

# S3类型
> otype(data.frame())
[1] "S3"

# 自定的S3类型
> x <- 1
> attr(x,'class')<-'foo'
> is.object(x)
[1] TRUE
> otype(x)
[1] "S3"

# S4类型
> setClass("Person",slots=list(name="character",age="numeric"))
> alice<-new("Person",name="Alice",age=40)
> isS4(alice)
[1] TRUE
> otype(alice)
[1] "S4"

# RC类型
> Account<-setRefClass("Account")
> a<-Account$new()
> class(a)
[1] "Account"
attr(,"package")
[1] ".GlobalEnv"

> is.object(a)
[1] TRUE
> isS4(a)
[1] TRUE
> otype(a)
[1] "RC"
```

判断函数类型：通过ftype()函数可以很容易的分辨出function,primitive,S3,S4,internal类型的函数，比起内置的类型检查要高效的多。

```{bash}
# 标准函数
> ftype(`%in%`)
[1] "function"

# primitive函数
> ftype(sum)
[1] "primitive" "generic"

# internal函数
> ftype(writeLines)
[1] "internal"
> ftype(unlist)
[1] "internal" "generic"

# S3函数
>  ftype(t.data.frame)
[1] "s3"     "method"
> ftype(t.test)
[1] "s3"      "generic"

# S4 函数
> setGeneric("union")
[1] "union"
> setMethod("union",c(x="data.frame",y="data.frame"),function(x, y){unique(rbind (x, y))})
[1] "union"
> ftype(union)
[1] "s4"      "generic"

# RC函数
> Account<-setRefClass("Account",fields=list(balance="numeric"),methods=list(
+   withdraw=function(x){balance<<-balance-x},
+   deposit=function(x){balance<<-balance+x}))
> a<-Account$new(balance=100)
> a$deposit(100)
> ftype(a$deposit)
[1] "rc"     "method"
```

### 3.6 查看对象底层的C语言类型 address(), refs(), typename()

我们可以通过address(), refs(), typename()来查看，R对象对应的底层C语言实现的类型。

+ typename: 返回C语言类型名
+ address: 返回内存地址
+ refs: 返回指针数字

查看变量

```{bash}
# 定义一个变量x
>  x <- 1:10

# 打印C语言类型名
> typename(x)
[1] "INTSXP"

# 返回指针
> refs(x)
[1] 1

# 打印内存地址
> address(x)
[1] "0x365f560"

# 定义一个list对象
>  z <- list(1:10)

# 打印C语言类型名
>  typename(z)
[1] "VECSXP"

# 延迟赋值
> delayedAssign("a", 1 + 2)

# 打印C语言类型名
> typename(a)
[1] "PROMSXP"

# 打印a变量
> a
[1] 3
> typename(a)
[1] "PROMSXP"

# 定义变量b，与a变量对比
> b<-3
> typename(b)
[1] "REALSXP"
```

### 3.7 查看对象是否被修改track_copy()

使用track_copy()函数，我们可以跟踪对象，并检查是被修改过，通过内存地址进行判断。

```{bash}
# 定义一个变量
> a<-1:3
> a
[1] 1 2 3

# 查看变量的内存地址
> address(a)
[1] "0x2ad77f0"

# 跟踪变量
> track_a <- track_copy(a)

# 检查变更是否被修改，没有修改
> track_a()

# 给变量赋值
> a[3] <- 3L

# 查看变量的内存地址，发现没有变化
> address(a)
[1] "0x2ad77f0"

# 检查变量是否被修改，没有修改
>  track_a()

# 再次给变量赋值
> a[3]<-3

# 查看变量的内存地址，内存地址改变
> address(a)
[1] "0x37f8580"

# 检查变量是否被修改，已被修改，变成一份copy
>  track_a()
a copied
```

### 3.8 查看闭包函数变量 unenclose()

使用unenclose()给闭包环境的变量的赋值

```{bash}
# 定义一个嵌套函数power
>  power <- function(exp) {
+      function(x) x ^ exp
+  }

# 调用闭包函数
>  square <- power(2)
>  cube <- power(3)

# 查看square函数，exp变量并显示没有赋值后的结果
> square
function(x) x ^ exp
<environment: 0x4055f28>

# 查看square函数，exp变量显示赋值后的结果
> unenclose(square)
function (x)
x^2

# 执行square函数
> square(3)
[1] 9
```

### 3.9 批量修改对象 modify_lang()

这是一个神奇的函数，可以方便地替换 list对象、表达式、函数 中的变量定义。

接下来，我们尝试替换list对象中定义的变量a为变量b

```{bash}
# 定义list对象及内部数据
> examples <- list(
+        quote(a <- 5),
+        alist(a = 1, c = a),
+        function(a = 1) a * 10,
+        expression(a <- 1, a, f(a), f(a = a))
+      )

# 查看对象数据
> examples
[[1]]
a <- 5

[[2]]
[[2]]$a
[1] 1

[[2]]$c
a

[[3]]
function (a = 1)
a * 10

[[4]]
expression(a <- 1, a, f(a), f(a = a))

# 定义转换函数a_to_b，
>  a_to_b <- function(x) {
+        if (is.name(x) && identical(x, quote(a))) return(quote(b))
+        x
+  }

# 批量修改对象，替换examples对象中，所有的变量a变成变量b
> modify_lang(examples, a_to_b)
[[1]]
b <- 5

[[2]]
[[2]]$a
[1] 1

[[2]]$c
b

[[3]]
function (a = 1)
b * 10

[[4]]
expression(b <- 1, b, f(b), f(a = b))
```

### 3.10 快速创建list对象 dots(), named_dots()

使用dots()函数，我们可以快速创建list对象，通过参数设置来list的数据的名字和值。

```{bash}
# 初始化一个变量
> y <- 2

# 创建list对象
> dots(x = 1, y, z = )
$x
[1] 1

[[2]]
y

$z

# 查看对象类型
> class(dots(x = 1, y, z = ))
[1] "list"

# 查看对象的内部结果
> str(dots(x = 1, y, z = ))
List of 3
 $ x: num 1
 $  : symbol y
 $ z: symbol
```

使用named_dots()函数，同样我们可以快速创建list对象，通过参数设置list的数据的名字和值。与dots()函数的不同点在于，参数变量就是list的数据的名字，如 变量y在没有赋值情况下，也被用作list数据的名字，并可以通过$y来得到值。

```{bash}
# 创建list对象
> named_dots(x = 1, y, z =)
$x
[1] 1

$y
y

$z

# 查看对象类型
> class(named_dots(x = 1, y, z =))
[1] "list"

# 查看对象的内部结果
> str(named_dots(x = 1, y, z =))
List of 3
 $ x: num 1
 $ y: symbol y
 $ z: symbol
 ```

### 3.11 查找符合条件函数 fun_calls()

使用fun_calls()函数，可以通过过滤条件快速找到函数。

查找base包中所有的函数，找到匹配match.fun字符串的函数名

```{bash}
> find_funs("package:base", fun_calls, "match.fun", fixed = TRUE)
Using environment package:base
 [1] "apply"  "eapply" "Find"   "lapply" "Map"    "mapply" "Negate" "outer"
 [9] "Reduce" "sapply" "sweep"  "tapply" "vapply"

# 查看Map函数，检查是否包括match.fun字符串
> Map
function (f, ...)
{
    f <- match.fun(f)
    mapply(FUN = f, ..., SIMPLIFY = FALSE)
}
<bytecode: 0x21688e0>
<environment: namespace:base>
```

查找stats包中所有的函数的参数，找到精确匹配FUN字符串的函数名

```{bash}
> find_funs("package:stats", fun_args, "^FUN$")
Using environment package:stats
[1] "addmargins"           "aggregate.data.frame" "aggregate.ts"
[4] "ave"                  "dendrapply"

# 查看ave函数源代码，检查参数名是否有FUN字符串
> ave
function (x, ..., FUN = mean)
{
    if (missing(...))
        x[] <- FUN(x)
    else {
        g <- interaction(...)
        split(x, g) <- lapply(split(x, g), FUN)
    }
    x
}
<bytecode: 0x2acba70>
<environment: namespace:stats>
```

### 3.12 查询环境变量 where(), rls(), parenv()

使用where()函数，可以定位对象的在R环境中的位置，有点像Linux的命令whereis。

```{bash}
# 定义一个变量x
> x <- 1
> where("x")
<environment: R_GlobalEnv>

# 查询t.test函数的位置
> where("t.test")
<environment: package:stats>
attr(,"name")
[1] "package:stats"
attr(,"path")
[1] "/usr/lib/R/library/stats"

> t.test
function (x, ...)
UseMethod("t.test")
<bytecode: 0x1ae9bc8>
<environment: namespace:stats>

# 查询mean函数的位置
> where("mean")
<environment: base>

# 查询where函数的位置
> where("where")
<environment: package:pryr>
attr(,"name")
[1] "package:pryr"
attr(,"path")
[1] "/home/conan/R/x86_64-pc-linux-gnu-library/3.0/pryr"
```

使用rls()函数，可以显示出当前环境的所有变量，包括当前变量，全局变量，空环境变量，命令空间环境变量。

```{bash}
# 打印当前环境的变量
> ls()
 [1] "a"                "Account"          "alice"            "a_to_b"
 [5] "b"                "compact1"         "compact2"         "examples"
 [9] "f"                "f1"               "f2"               "g"
[13] "myGeneric"        "my_long_variable" "plot2"            "union"
[17] "x"                "y"

# 打印所有环境的变量
> rls()
[[1]]
 [1] "a"                          "Account"
 [3] "alice"                      "a_to_b"
 [5] "b"                          ".__C__Account"
 [7] "compact1"                   "compact2"
 [9] ".__C__Person"               "examples"
[11] "f"                          "f1"
[13] "f2"                         "g"
[15] ".__global__"                "myGeneric"
[17] "my_long_variable"           "plot2"
[19] ".Random.seed"               ".requireCachedGenerics"
[21] ".__T__myGeneric:.GlobalEnv" ".__T__union:base"
[23] "union"                      "x"
[25] "y"
```

使用parenv()函数，可以找到函数调用的上一级环境，从而可以追溯到函数的根。

```{bash}
# 定义一个3层嵌套函数
> adder <- function(x) function(y) function(z) x + y + z

# 调用第一层函数
> add2 <- adder(2)

# 查看函数
> add2
function(y) function(z) x + y + z
<environment: 0x323c000>

# 调用第二层函数
> add3<-add2(3)
> add3
function(z) x + y + z
<environment: 0x3203558>

# 查内层函数的上一级环境
>  parenv(add3)
<environment: 0x323c000>
>  parenv(add2)
<environment: R_GlobalEnv>
```

### 3.13 找印调用关系 call_tree(), ast()

使用call_tree()函数，可以打印出表达式的调用关系

```{bash}
# 嵌套函数语句调用
>  call_tree(quote(f(x, 1, g(), h(i()))))
\- ()
  \- `f
  \- `x
  \-  1
  \- ()
    \- `g
  \- ()
    \- `h
    \- ()
      \- `i

# 条件语句调用
> call_tree(quote(if (TRUE) 3 else 4))
\- ()
  \- `if
  \-  TRUE
  \-  3
  \-  4

# 表达式语句调用
> call_tree(expression(1, 2, 3))
\-  1
\-  2
\-  3
```

使用ast()函数，可以直接打印语句的调用关系

```{bash}
# 嵌套表达式语句
> ast(f(x, 1, g(), h(i())))
\- ()
  \- `f
  \- `x
  \-  1
  \- ()
    \- `g
  \- ()
    \- `h
    \- ()
      \- `i

# 条件语句
> ast(if (TRUE) 3 else 4)
\- ()
  \- `if
  \-  TRUE
  \-  3
  \-  4

# 函数定义
> ast(function(a = 1, b = 2) {a + b})
\- ()
  \- `function
  \- []
    \ a = 1
    \ b = 2
  \- ()
    \- `{
    \- ()
      \- `+
      \- `a
      \- `b
  \-

# 函数调用
> ast(f()()())
\- ()
  \- ()
    \- ()
      \- `f
```

### 3.14 promise对象 uneval(), is_promise()

promise对象：是R语言中延迟加载机制的一部分，包含三个部分：值，表达式和环境。当函数被调用时参数进行匹配，然后每个形式参数会绑定到一个promise上。表达式有形式参数和存储在promise里的函数的指针。

简单来说，延迟加载调用过程就是，先把函数指针存储在promise对象里，并不马上调用；当其实调用发生时，从promise对象里找到函数指针，进行函数的调用。

```{bash}
# 定义变量并赋值
> x <- 10

# 检查是否 promise模式
> is_promise(x)
[1] FALSE

# 匿名函数调用，检查是否 promise模式
> (function(x) is_promise(x))(x = 10)
[1] TRUE
```

使用uneval()函数，可以在延迟赋值的过程中，打印函数调用方法，而不执行赋值函数调用。

```{bash}
# 定义一个函数
> f <- function(x) {
+     uneval(x)
+ }

# 打印函数调用
> f(a + b)
a + b

> class(f(a+b))
[1] "call"

# 打印函数调用
> f(1 + 4)
1 + 4

# 延迟赋值
> delayedAssign("x", 1 + 4)

# 不执行函数调用，只打印函数调用
> uneval(x)
1 + 4

# 执行函数调用，并赋值
> x
[1] 5

# 延迟赋值又一例
> delayedAssign("x", {
+     for(i in 1:3)
+         cat("yippee!\n")
+     10
+ })

# 执行函数调用，并赋值
> x
yippee!
yippee!
yippee!
[1] 10
```

### 3.15 数据绑定%<a-%, %<c-%，%<d-%, rebind,<<-

使用特殊的函数，可以实现数据绑定的功能。

直接绑定

```{bash}
> x %<a-% runif(1)
> x
[1] 0.06793592
> x
[1] 0.8217227
```

常量绑定

```{bash}
> y %<c-% 4 + 2
[1] 6
> y
[1] 4
```

延迟绑定

```{bash}
> z %<d-% (a + b)
> a <- 10
> b <- 100
> z
[1] 110
```

重新绑定

```{bash}
# 对已知变量a重新赋值
> a <- 1
> rebind("a", 2)

# 对未知变量cc重新赋值，出错
> rebind("ccc", 2)
Error: Can't find ccc

# 用 <<- 对已知变量a重新赋值
> a<<-2
> a
[1] 2

# 用 <<- 对未知变量cc重新赋值
> rm(ccc)
> ccc
Error: object 'ccc' not found
> ccc<<-2
> ccc
[1] 2
```

通过对pryr全面介绍，我们了解这个包的强大，对于R的数据结构的理解非常有帮助。

#### 转载请注明出处：http://blog.fens.me/r-pryr/