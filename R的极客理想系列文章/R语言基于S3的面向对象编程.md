R语言基于S3的面向对象编程
===========

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-class-s3/

![R语言基于S3的面向对象编程](http://blog.fens.me/wp-content/uploads/2014/04/r-class-s3.png)

#### 前言

对于R语言的面向对象编程，不同于其他的编程语言，R语言提供了3种底层对象类型，一种是S3类型，一种是S4类型，还有一种是RC类型。

S3对象简单、具有动态性、结构化特征不明显；S4对象结构化、 功能强大；RC对象是2.12版本后使用的新类型，用于解决S3,S4很难实现的对象。

本文将从S3对象开始，介绍R语言面向对象编程的细节。

#### 目录

1 S3对象介绍
2 创建S3对象
3 泛型函数和方法调用
4 查看S3对象的函数
5 S3对象继承
6 S3对象的缺点
7 S3对象的使用

## 1 S3对象介绍

在R语言中，基于S3对象的面向对象编程，是一种基于泛型函数的实现方式。泛型函数是一种特殊的函数, 根据传入对象的类型决定调用哪个具体的方法。基于S3对象实现的面向对象编程，不同其他语言的面向对象编程，是一种动态函数调用的模拟实现。S3对象被广泛应用于R的早期的开发包中。

关于面向对象的介绍，请参考文章：<a title="R语言面向对象编程" href="http://blog.fens.me/r-object-oriented-intro/" target="_blank">R语言面向对象编程</a>

## 2 创建S3对象

本文的系统环境

* Linux: Ubuntu Server 12.04.2 LTS 64bit
* R: 3.0.1 x86_64-pc-linux-gnu

> 注：pryr只支持Linux系统环境


为了方便我们检查对象的类型，引入pryr包作为辅助工具。关于pryr包的介绍，请参考文章:[撬动R内核的高级工具包pryr](http://blog.fens.me/r-pryr/)

```{bash}
# 加载pryr包
> library(pryr)
```

### 通过变量创建S3对象
```{bash}
> x<-1
> attr(x,'class')<-'foo'

> x
[1] 1
attr(,"class")
[1] "foo"

> class(x)
[1] "foo"

# 用pryr包的otype函数，检查x的类型
> otype(x)
[1] "S3"
```

通过structure()函数创建S3对象
```{bash}
> y <- structure(2, class = "foo")

> y
[1] 2
attr(,"class")
[1] "foo"

> class(y)
[1] "foo"

> otype(y)
[1] "S3"
```

### 创建一个多类型的S3对象

S3对象没有明确结构关系，一个S3对象可以有多个类型, S3对象的 class 属性可以是一个向量，包括多种类型。

```{bash}
> x<-1
> attr(x,'class')<- c("foo", "bar")
> class(x)
[1] "foo" "bar"
> otype(x)
[1] "S3"
```

## 3 泛型函数和方法调用

对于S3对象的使用，通常用UseMethod()函数来定义一个泛型函数的名称，通过传入参数的class属性，来确定不同的方法调用。

定义一个teacher的泛型函数

+ 用UseMethod()定义teacher泛型函数
+ 用teacher.xxx的语法格式定义teacher对象的行为
+ 其中teacher.default是默认行为

```{bash}
# 用UseMethod()定义teacher泛型函数
> teacher <- function(x, ...) UseMethod("teacher")

# 用pryr包中ftype()函数，检查teacher的类型
> ftype(teacher)
[1] "s3"      "generic"

# 定义teacher内部函数
> teacher.lecture <- function(x) print("讲课")
> teacher.assignment <- function(x) print("布置作业")
> teacher.correcting <- function(x) print("批改作业")
> teacher.default<-function(x) print("你不是teacher")
```

方法调用时，通过传入参数的class属性，来确定不同的方法调用。

+ 定义一个变量a，并设置a的class属性为lecture
+ 把变量a，传入到teacher泛型函数中
+ 函数teacher.lecture()函数的行为被调用

```{bash}
> a<-'teacher'

# 给老师变量设置行为
> attr(a,'class') <- 'lecture'

# 执行老师的行为
> teacher(a)
[1] "讲课"
```

当然，我们也可以直接调用teacher中定义的行为，如果这样做了就失败了面向对象封装的意义。

```{bash}
> teacher.lecture()
[1] "讲课"

> teacher.lecture(a)
[1] "讲课"

> teacher()
[1] "你不是teacher"
```

## 4  查看S3对象的函数

当我们使用S3对象进行面向对象封装后，可以用methods()函数来查看S3对象中的定义的内部行为函数。

```{bash}
# 查看teacher对象
> teacher
function(x, ...) UseMethod("teacher")

# 查看teacher对象的内部函数
>  methods(teacher)
[1] teacher.assignment teacher.correcting teacher.default    teacher.lecture
```

通过methods()的generic.function参数，来匹配泛型函数名字。

```{bash}
> methods(generic.function=predict)
 [1] predict.ar*                predict.Arima*             predict.arima0*
 [4] predict.glm                predict.HoltWinters*       predict.lm
 [7] predict.loess*             predict.mlm                predict.nls*
[10] predict.poly               predict.ppr*               predict.prcomp*
[13] predict.princomp*          predict.smooth.spline*     predict.smooth.spline.fit*
[16] predict.StructTS*

   Non-visible functions are asterisked
```

通过methods()的class参数，来匹配类的名字。

```{bash}
> methods(class=lm)
 [1] add1.lm*           alias.lm*          anova.lm           case.names.lm*
 [5] confint.lm*        cooks.distance.lm* deviance.lm*       dfbeta.lm*
 [9] dfbetas.lm*        drop1.lm*          dummy.coef.lm*     effects.lm*
[13] extractAIC.lm*     family.lm*         formula.lm*        hatvalues.lm
[17] influence.lm*      kappa.lm           labels.lm*         logLik.lm*
[21] model.frame.lm     model.matrix.lm    nobs.lm*           plot.lm
[25] predict.lm         print.lm           proj.lm*           qr.lm*
[29] residuals.lm       rstandard.lm       rstudent.lm        simulate.lm*
[33] summary.lm         variable.names.lm* vcov.lm*

   Non-visible functions are asterisked
```

用getAnywhere()函数，查看所有的函数。
```{bash}
# 查看teacher.lecture函数
> getAnywhere(teacher.lecture)
A single object matching ‘teacher.lecture’ was found
It was found in the following places
  .GlobalEnv
  registered S3 method for teacher
with value

function(x) print("讲课")

# 查看不可见的函数predict.ppr
> predict.ppr
Error: object 'predict.ppr' not found
> exists("predict.ppr")
[1] FALSE

# getAnywhere()函数查找predict.ppr
> getAnywhere("predict.ppr")
A single object matching ‘predict.ppr’ was found
It was found in the following places
  registered S3 method for predict from namespace stats
  namespace:stats
with value

function (object, newdata, ...)
{
    if (missing(newdata))
        return(fitted(object))
    if (!is.null(object$terms)) {
        newdata <- as.data.frame(newdata)
        rn <- row.names(newdata)
        Terms <- delete.response(object$terms)
        m <- model.frame(Terms, newdata, na.action = na.omit,
            xlev = object$xlevels)
        if (!is.null(cl <- attr(Terms, "dataClasses")))
            .checkMFClasses(cl, m)
        keep <- match(row.names(m), rn)
        x <- model.matrix(Terms, m, contrasts.arg = object$contrasts)
    }
    else {
        x <- as.matrix(newdata)
        keep <- seq_len(nrow(x))
        rn <- dimnames(x)[[1L]]
    }
    if (ncol(x) != object$p)
        stop("wrong number of columns in 'x'")
    res <- matrix(NA, length(keep), object$q, dimnames = list(rn,
        object$ynames))
    res[keep, ] <- matrix(.Fortran(C_pppred, as.integer(nrow(x)),
        as.double(x), as.double(object$smod), y = double(nrow(x) *
            object$q), double(2 * object$smod[4L]))$y, ncol = object$q)
    drop(res)
}
<bytecode: 0x000000000df6c2d0>
<environment: namespace:stats>
```

使用getS3method()函数，也同样可以查看不可见的函数
```{bash}
# getS3method()函数查找predict.ppr
> getS3method("predict", "ppr")
function (object, newdata, ...)
{
    if (missing(newdata))
        return(fitted(object))
    if (!is.null(object$terms)) {
        newdata <- as.data.frame(newdata)
        rn <- row.names(newdata)
        Terms <- delete.response(object$terms)
        m <- model.frame(Terms, newdata, na.action = na.omit,
            xlev = object$xlevels)
        if (!is.null(cl <- attr(Terms, "dataClasses")))
            .checkMFClasses(cl, m)
        keep <- match(row.names(m), rn)
        x <- model.matrix(Terms, m, contrasts.arg = object$contrasts)
    }
    else {
        x <- as.matrix(newdata)
        keep <- seq_len(nrow(x))
        rn <- dimnames(x)[[1L]]
    }
    if (ncol(x) != object$p)
        stop("wrong number of columns in 'x'")
    res <- matrix(NA, length(keep), object$q, dimnames = list(rn,
        object$ynames))
    res[keep, ] <- matrix(.Fortran(C_pppred, as.integer(nrow(x)),
        as.double(x), as.double(object$smod), y = double(nrow(x) *
            object$q), double(2 * object$smod[4L]))$y, ncol = object$q)
    drop(res)
}
<bytecode: 0x000000000df6c2d0>
<environment: namespace:stats>
```

## 5 S3对象的继承关系

S3对象有一种非常简单的继承方式，用NextMethod()函数来实现。

定义一个 node泛型函数
```{bash}
> node <- function(x) UseMethod("node", x)
> node.default <- function(x) "Default node"

# father函数
> node.father <- function(x) c("father")

# son函数，通过NextMethod()函数指向father函数
> node.son <- function(x) c("son", NextMethod())

# 定义n1
> n1 <- structure(1, class = c("father"))
# 在node函数中传入n1，执行node.father()函数
> node(n1)
[1] "father"

# 定义n2，设置class属性为两个
> n2 <- structure(1, class = c("son", "father"))
# 在node函数中传入n2，执行node.son()函数和node.father()函数
> node(n2)
[1] "son"    "father"
```

通过对node()函数传入n2的参数，node.son()先被执行，然后通过NextMethod()函数继续执行了node.father()函数。这样其实就模拟了，子函数调用父函数的过程，实现了面向对象编程中的继承。

## 6 S3对象的缺点

从上面对S3对象的介绍来看，S3对象并不是完全的面向对象实现，而是一种通过泛型函数模拟的面向对象的实现。

+ S3使用起来简单，但在实际的面向对象编程过程中，当对象关系有一定的复杂度，S3对象所表达的意义就会变得不太清楚。
+ S3封装的内部函数，可绕过泛型函数的检查，以直接被调用。
+ S3参数的class属性，可以被任意设置，没有预处理的检查。
+ S3参数，只能通过调用class属性进行函数调用，其他属性则不会被class()函数执行。
+ S3参数的class属性有多个值时，调用时会按照程序赋值顺序来调用第一个合法的函数。

所以，S3只能R语言面向对象的一种简单的实现。

## 7 S3对象的使用

S3对象系统，被广泛地应用于R语言的早期开发中。在base包中，就有很多的S3对象。

base包的S3对象

```{bash}
# mean函数
> mean
function (x, ...)
UseMethod("mean")
<bytecode: 0x3f8e0f0>
<environment: namespace:base>
> ftype(mean)
[1] "s3"      "generic"

# t函数
> ftype(t)
[1] "s3"      "generic"

# plot函数
> ftype(plot)
[1] "s3"      "generic"
```

自定义的S3对象
```{bash}
# 定义数字型变量a
> a <- 1
# 变量a的class为numeric
> class(a)
[1] "numeric"

# 定义泛型函数f1
> f1 <- function(x) {
+   a <- 2
+   UseMethod("f1")
+ }

# 定义f1的内部函数
> f1.numeric <- function(x) a

# 给f1()传入变量a
> f1(a)
[1] 2

# 给f1()传入数字99
> f1(99)
[1] 2

# 定义f1的内部函数
> f1.character <- function(x) paste("char", x)

# 给f1()传入字符a
> f1("a")
[1] "char a"
```

这样，我们就对S3对象系统有了一个全面认识，开始R语言的面向对象编程之路。

#### 转载请注明出处：http://blog.fens.me/r-class-s3/