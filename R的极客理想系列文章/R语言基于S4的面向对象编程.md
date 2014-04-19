R语言基于S4的面向对象编程
===========

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-class-s4/

![R语言基于S4的面向对象编程](http://blog.fens.me/wp-content/uploads/2014/04/r-class-s4.png)

#### 前言

本文接上一篇文章 [R语言基于S3的面向对象编程](http://blog.fens.me/r-class-s3/)，本文继续介绍R语言基于S4的面向对象编程。

S4对象系统具有明显的结构化特征，更适合面向对象的程序设计。Bioconductor社区，以S4对象系统做为基础架构，只接受符合S4定义的R包。

#### 目录

1. S4对象介绍
2. 创建S4对象
3. 访问对象的属性
4. S4的泛型函数
5. 查看S4对象的函数
6. S4对象的使用

## 1 S4对象介绍

S4对象系统是一种标准的R语言面向对象实现方式，S4对象有明确的类定义，参数定义，参数检查，继承关系，实例化等的面向对象系统的特征。

## 2 创建S4对象

本文的系统环境

* Linux: Ubuntu Server 12.04.2 LTS 64bit
* R: 3.0.1 x86_64-pc-linux-gnu

为了方便我们检查对象的类型，引入pryr包作为辅助工具。关于pryr包的介绍，请参考文章:[撬动R内核的高级工具包pryr](http://blog.fens.me/r-pryr/)

```{bash}
# 加载pryr包
> library(pryr)
```

### 2.1 如何创建S4对象？

由于S4对象是标准的面向对象实现方式， 有专门的类定义函数 setClass() 和类的实例化函数new() ，我们看一下setClass()和new()是如何动作的。

2.1.1 setClass()

查看setClass的函数定义

```{bash}
setClass(Class, representation, prototype, contains=character(),
          validity, access, where, version, sealed, package,
          S3methods = FALSE, slots)
```

参数列表：

+ Class: 定义类名
+ slots: 定义属性和属性类型
+ prototype: 定义属性的默认值
+ contains=character(): 定义父类，继承关系
+ validity: 定义属性的类型检查
+ where: 定义存储空间
+ sealed: 如果设置TRUE，则同名类不能被再次定义
+ package: 定义所属的包
+ S3methods: R3.0.0不建议使用
+ representation R3.0.0不建议使用
+ access R3.0.0不建议使用
+ version R3.0.0不建议使用

### 2.2 创建一个S4对象实例
```{bash}
# 定义一个S4对象
> setClass("Person",slots=list(name="character",age="numeric"))

# 实例化一个Person对象
> father<-new("Person",name="F",age=44)

# 查看father对象，有两个属性name和age
> father
An object of class "Person"
Slot "name":
[1] "F"

Slot "age":
[1] 44

# 查看father对象类型，为Person
> class(father)
[1] "Person"
attr(,"package")
[1] ".GlobalEnv"

# 查看father对象为S4的对象
> otype(father)
[1] "S4"
```

### 2.3 创建一个有继承关系的S4对象
```{bash}
# 创建一个S4对象Person
> setClass("Person",slots=list(name="character",age="numeric"))

# 创建Person的子类
> setClass("Son",slots=list(father="Person",mother="Person"),contains="Person")

# 实例化Person对象
> father<-new("Person",name="F",age=44)
> mother<-new("Person",name="M",age=39)

# 实例化一个Son对象
> son<-new("Son",name="S",age=16,father=father,mother=mother)

# 查看son对象的name属性
> son@name
[1] "S"

# 查看son对象的age属性
> son@age
[1] 16

# 查看son对象的father属性
> son@father
An object of class "Person"
Slot "name":
[1] "F"

Slot "age":
[1] 44

# 查看son对象的mother属性
> slot(son,"mother")
An object of class "Person"
Slot "name":
[1] "M"

Slot "age":
[1] 39

# 检查son类型
> otype(son)
[1] "S4"

# 检查son@name属性类型
> otype(son@name)
[1] "primitive"

# 检查son@mother属性类型
> otype(son@mother)
[1] "S4"

# 用isS4()，检查S4对象的类型
> isS4(son)
[1] TRUE
> isS4(son@name)
[1] FALSE
> isS4(son@mother)
[1] TRUE
```

### 2.4 S4对象的默认值
```{bash}
> setClass("Person",slots=list(name="character",age="numeric"))

# 属性age为空
> a<-new("Person",name="a")
> a
An object of class "Person"
Slot "name":
[1] "a"

Slot "age":
numeric(0)

# 设置属性age的默认值20
> setClass("Person",slots=list(name="character",age="numeric"),prototype = list(age = 20))

# 属性age为空
> b<-new("Person",name="b")

# 属性age的默认值是20
> b
An object of class "Person"
Slot "name":
[1] "b"

Slot "age":
[1] 20
```

### 2.5 S4对象的类型检查
```{bash}
> setClass("Person",slots=list(name="character",age="numeric"))

# 传入错误的age类型
> bad<-new("Person",name="bad",age="abc")
Error in validObject(.Object) :
  invalid class “Person” object: invalid object for slot "age" in class "Person": got class "character", should be or extend class "numeric"

# 设置age的非负检查
> setValidity("Person",function(object) {
+     if (object@age <= 0) stop("Age is negative.")
+ })
Class "Person" [in ".GlobalEnv"]

Slots:
Name:       name       age
Class: character   numeric

# 修传入小于0的年龄
> bad2<-new("Person",name="bad",age=-1)
Error in validityMethod(object) : Age is negative.
```

### 2.6 从一个旧对象中创建新对象

S3对象，还支持从一个旧的对象中创建新对象，创建时可以覆盖旧对象的值
```{bash}
> setClass("Person",slots=list(name="character",age="numeric"))

# 创建一个对象实例n1
> n1<-new("Person",name="n1",age=19);n1
An object of class "Person"
Slot "name":
[1] "n1"

Slot "age":
[1] 19

# 从实例n1中，创建实例n2，并修改name的属性值
> n2<-initialize(n1,name="n2");n2
An object of class "Person"
Slot "name":
[1] "n2"

Slot "age":
[1] 19
```

## 3 访问对象的属性

在S3对象中，一般我使用$来访问一个对象的属性，但在S4对象中，我们只能使用@来访问一个对象的属性

```{bash}
> setClass("Person",slots=list(name="character",age="numeric"))
> a<-new("Person",name="a")

# 访问S4对象的属性
> a@name
[1] "a"
> slot(a, "name")
[1] "a"

# 错误的属性访问
> a$name
Error in a$name : $ operator not defined for this S4 class
> a[1]
Error in a[1] : object of type 'S4' is not subsettable
> a[[1]]
Error in a[[1]] : this S4 class is not subsettable
```

## 4 S4的泛型函数

S4的泛型函数实现有别于S3的实现，S4分离了方法的定义和实现，如在其他语言中我们常说的接口和实现分离。通过setGeneric()来定义接口，通过setMethod()来定义现实类。这样可以让S4对象系统，更符合面向对象的特征。

普通函数的定义和调用
```{bash}
> work<-function(x) cat(x, "is working")
> work('Conan')
Conan is working
```

让我来看看如何用R分离接口和现实
```{bash}
# 定义Person对象
> setClass("Person",slots=list(name="character",age="numeric"))

# 定义泛型函数work，即接口
> setGeneric("work",function(object) standardGeneric("work"))
[1] "work"

# 定义work的现实，并指定参数类型为Person对象
> setMethod("work", signature(object = "Person"), function(object) cat(object@name , "is working") )
[1] "work"

# 创建一个Person对象a
> a<-new("Person",name="Conan",age=16)

# 把对象a传入work函数
> work(a)
Conan is working
```

通过S4对象系统，把原来的函数定义和调用2步，为成了4步进行：

+ 定义数据对象类型
+ 定义接口函数
+ 定义实现函数
+ 把数据对象以参数传入到接口函数，执行实现函数

通过S4对象系统，是一个结构化的，完整的面向对象实现。

## 5 查看S4对象的函数

当我们使用S4对象进行面向对象封装后，我们还需要能查看到S4对象的定义和函数定义。

还以上节中Person和work的例子
```{bash}
# 检查work的类型
> ftype(work)
[1] "s4"      "generic"

# 直接查看work函数
> work
standardGeneric for "work" defined from package ".GlobalEnv"
function (object)
standardGeneric("work")
<environment: 0x2aa6b18>
Methods may be defined for arguments: object
Use  showMethods("work")  for currently available ones.

# 查看work函数的现实定义
> showMethods(work)
Function: work (package .GlobalEnv)
object="Person"

# 查看Person对象的work函数现实
> getMethod("work", "Person")
Method Definition:
function (object)
cat(object@name, "is working")
Signatures:
        object
target  "Person"
defined "Person"

> selectMethod("work", "Person")
Method Definition:
function (object)
cat(object@name, "is working")
Signatures:
        object
target  "Person"
defined "Person"

# 检查Person对象有没有work函数
>  existsMethod("work", "Person")
[1] TRUE
> hasMethod("work", "Person")
[1] TRUE
```

## 6 S4对象的使用

我们接下用S4对象做一个例子，定义一组图形函数的库。

### 6.1 任务一：定义图形库的数据结构和计算函数

假设最Shape为图形的基类，包括圆形(Circle)和椭圆形(Ellipse)，并计算出它们的面积(area)和周长(circum)。

+ 定义图形库的数据结构
+ 定义圆形的数据结构，并计算面积和周长
+ 定义椭圆形的数据结构，并计算面积和周长

如图所示结构：

![](http://blog.fens.me/wp-content/uploads/2014/04/s4-shape1.png)

定义基类Shape 和 圆形类Circle
```{bash}
# 定义基类Shape
> setClass("Shape",slots=list(name="character"))

# 定义圆形类Circle，继承Shape，属性radius默认值为1
> setClass("Circle",contains="Shape",slots=list(radius="numeric"),prototype=list(radius = 1))

# 验证radius属性值要大等于0
> setValidity("Circle",function(object) {
+     if (object@radius <= 0) stop("Radius is negative.")
+ })
Class "Circle" [in ".GlobalEnv"]
Slots:
Name:     radius      name
Class:   numeric character
Extends: "Shape"

# 创建两个圆形实例
> c1<-new("Circle",name="c1")
> c2<-new("Circle",name="c2",radius=5)
```

定义计算面积的接口和现实
```{bash}
# 计算面积泛型函数接口
> setGeneric("area",function(obj,...) standardGeneric("area"))
[1] "area"

# 计算面积的函数现实
> setMethod("area","Circle",function(obj,...){
+     print("Area Circle Method")
+     pi*obj@radius^2
+ })
[1] "area"

# 分别计算c1和c2的两个圆形的面积
> area(c1)
[1] "Area Circle Method"
[1] 3.141593
> area(c2)
[1] "Area Circle Method"
[1] 78.53982
```

定义计算周长的接口和现实

```{bash}
# 计算周长泛型函数接口
> setGeneric("circum",function(obj,...) standardGeneric("circum"))
[1] "circum"

# 计算周长的函数现实
> setMethod("circum","Circle",function(obj,...){
+     2*pi*obj@radius
+ })

# 分别计算c1和c2的两个圆形的面积
[1] "circum"
> circum(c1)
[1] 6.283185
> circum(c2)
[1] 31.41593
```

上面的代码，我们实现了圆形的定义，下来我们实现椭圆形。

```{bash}
# 定义椭圆形的类，继承Shape，radius参数默认值为c(1,1)，分别表示椭圆形的长半径和短半径
> setClass("Ellipse",contains="Shape",slots=list(radius="numeric"),prototype=list(radius=c(1,1)))

# 验证radius参数
> setValidity("Ellipse",function(object) {
+     if (length(object@radius) != 2 ) stop("It's not Ellipse.")
+     if (length(which(object@radius<=0))>0) stop("Radius is negative.")
+ })
Class "Ellipse" [in ".GlobalEnv"]
Slots:
Name:     radius      name
Class:   numeric character
Extends: "Shape"

# 创建两个椭圆形实例e1,e2
> e1<-new("Ellipse",name="e1")
> e2<-new("Ellipse",name="e2",radius=c(5,1))

# 计算椭圆形面积的函数现实
> setMethod("area", "Ellipse",function(obj,...){
+     print("Area Ellipse Method")
+     pi * prod(obj@radius)
+ })
[1] "area"

# 计算e1,e2两个椭圆形的面积
> area(e1)
[1] "Area Ellipse Method"
[1] 3.141593
> area(e2)
[1] "Area Ellipse Method"
[1] 15.70796

# 计算椭圆形周长的函数现实
> setMethod("circum","Ellipse",function(obj,...){
+     cat("Ellipse Circum :\n")
+     2*pi*sqrt((obj@radius[1]^2+obj@radius[2]^2)/2)
+ })
[1] "circum"

# 计算e1,e2两个椭圆形的周长
> circum(e1)
Ellipse Circum :
[1] 6.283185
> circum(e2)
Ellipse Circum :
[1] 22.65435
```

### 6.2 任务二：重构圆形和椭圆形的设计

上一步，我们已经完成了 圆形和椭圆形 的数据结构定义，以及计算面积和周长的方法现实。不知大家有没有发现，圆形是椭圆形的一个特例呢？

当椭圆形的长半径和短半径相等时，即radius的两个值相等，形成的图形为圆形。利用这个特点，我们就可以重新设计 圆形和椭圆形 的关系。椭圆形是圆形的父类，而圆形是椭圆形的子类。

如图所示结构：

![](http://blog.fens.me/wp-content/uploads/2014/04/s4-shape2.png)

```{bash}
# 基类Shape
> setClass("Shape",slots=list(name="character",shape="character"))

# Ellipse继承Shape
> setClass("Ellipse",contains="Shape",slots=list(radius="numeric"),prototype=list(radius=c(1,1),shape="Ellipse"))

# Circle继承Ellipse
> setClass("Circle",contains="Ellipse",slots=list(radius="numeric"),prototype=list(radius = 1,shape="Circle"))

# 定义area接口
> setGeneric("area",function(obj,...) standardGeneric("area"))
[1] "area"

# 定义area的Ellipse实现
> setMethod("area","Ellipse",function(obj,...){
+     cat("Ellipse Area :\n")
+     pi * prod(obj@radius)
+ })
[1] "area"

# 定义area的Circle实现
> setMethod("area","Circle",function(obj,...){
+     cat("Circle Area :\n")
+     pi*obj@radius^2
+ })
[1] "area"

# 定义circum接口
> setGeneric("circum",function(obj,...) standardGeneric("circum"))
[1] "circum"

# 定义circum的Ellipse实现
> setMethod("circum","Ellipse",function(obj,...){
+     cat("Ellipse Circum :\n")
+     2*pi*sqrt((obj@radius[1]^2+obj@radius[2]^2)/2)
+ })
[1] "circum"

# 定义circum的Circle实现
> setMethod("circum","Circle",function(obj,...){
+     cat("Circle Circum :\n")
+     2*pi*obj@radius
+ })
[1] "circum"

# 创建实例
> e1<-new("Ellipse",name="e1",radius=c(2,5))
> c1<-new("Circle",name="c1",radius=2)

# 计算椭圆形的面积和周长
> area(e1)
Ellipse Area :
[1] 31.41593
> circum(e1)
Ellipse Circum :
[1] 23.92566

# 计算圆形的面积和周长
> area(c1)
Circle Area :
[1] 12.56637
> circum(c1)
Circle Circum :
[1] 12.56637
```

我们重构后的结构，是不是会更合理呢！！

### 6.3 任务三：增加矩形的图形处理

我们的图形库，进一步扩充，需要加入矩形和正方形。

+ 定义矩形的数据结构，并计算面积和周长
+ 定义正方形的数据结构，并计算面积和周长
+ 正方形是矩形的特例，定义矩形是正方形的父类，而正方形是矩形的子类。

如图所示结构：

![](http://blog.fens.me/wp-content/uploads/2014/04/s4-shape3.png)

```{bash}
# 定义矩形Rectangle，继承Shape
> setClass("Rectangle",contains="Shape",slots=list(edges="numeric"),prototype=list(edges=c(1,1),shape="Rectangle"))

# 定义正方形Square，继承Rectangle
> setClass("Square",contains="Rectangle",slots=list(edges="numeric"),prototype=list(edges=1,shape="Square"))

# 定义area的Rectangle实现
> setMethod("area","Rectangle",function(obj,...){
+     cat("Rectangle Area :\n")
+     prod(obj@edges)
+ })
[1] "area"

# 定义area的Square实现
> setMethod("area","Square",function(obj,...){
+     cat("Square Area :\n")
+     obj@edges^2
+ })
[1] "area"

# 定义circum的Rectangle实现
> setMethod("circum","Rectangle",function(obj,...){
+     cat("Rectangle Circum :\n")
+     2*sum(obj@edges)
+ })
[1] "circum"

# 定义circum的Square实现
> setMethod("circum","Square",function(obj,...){
+     cat("Square Circum :\n")
+     4*obj@edges
+ })
[1] "circum"

# 创建实例
> r1<-new("Rectangle",name="r1",edges=c(2,5))
> s1<-new("Square",name="s1",edges=2)

# 计算矩形形的面积和周长
> area(r1)
Rectangle Area :
[1] 10
> area(s1)
Square Area :
[1] 4

# 计算正方形的面积和周长
> circum(r1)
Rectangle Circum :
[1] 14
> circum(s1)
Square Circum :
[1] 8
```

这样，我们的图形库，已经支持了4种图形了！用面向对象的结构来设计，是不是结构化思路很清晰呢！！


### 6.4 任务四：在基类Shape中，增加shape属性和getShape方法

接下来，要对图形库的所有图形，定义图形类型的变量shape，然后再提供一个getShape函数可以检查实例中的是shape变量。

这个需求，如果没有面向对象的结构，那么你需要在所有图形定义的代码中，都增加一个参数和一个判断，如果有100图形，改起来还是挺复杂的。而面向对象的程序设计，就非常容易解决这个需求。我们只需要在基类上改动代码就可以实现了。

如图所示结构：

![](http://blog.fens.me/wp-content/uploads/2014/04/s4-shape4.png)

```{bash}
# 重新定义基类Shape，增加shape属性
> setClass("Shape",slots=list(name="character",shape="character"))

# 定义getShape接口
> setGeneric("getShape",function(obj,...) standardGeneric("getShape"))
[1] "getShape"

# 定义getShape实现
> setMethod("getShape","Shape",function(obj,...){
+     cat(obj@shape,"\n")
+ })
[1] "getShape"
```

其实，这样改动一个就可以了，我们只需要重实例化每个图形的对象就行了。

```{bash}
# 实例化一个Square对象，并给shape属性赋值
> s1<-new("Square",name="s1",edges=2, shape="Square")

# 调用基类的getShape()函数
> getShape(r1)
Rectangle
```

是不是很容易的呢！在代码只在基类里修改了，所有的图形就有了对应的属性和方法。

如果我们再多做一步，可以修改每个对象的定义，增加shape属性的默认值。

```{bash}
setClass("Ellipse",contains="Shape",slots=list(radius="numeric"),prototype=list(radius=c(1,1),shape="Ellipse"))
setClass("Circle",contains="Ellipse",slots=list(radius="numeric"),prototype=list(radius = 1,shape="Circle"))
setClass("Rectangle",contains="Shape",slots=list(edges="numeric"),prototype=list(edges=c(1,1),shape="Rectangle"))
setClass("Square",contains="Rectangle",slots=list(edges="numeric"),prototype=list(edges=1,shape="Square"))
```

再实例化对象时，属性shape会被自动赋值
```{bash}
# 实例化一个Square对象
> s1<-new("Square",name="s1",edges=2)

# 调用基类的getShape()函数
> getShape(r1)
Rectangle
```

下面是完整的R语言的代码实现：

```{bash}
setClass("Shape",slots=list(name="character",shape="character"))
setClass("Ellipse",contains="Shape",slots=list(radius="numeric"),prototype=list(radius=c(1,1),shape="Ellipse"))
setClass("Circle",contains="Ellipse",slots=list(radius="numeric"),prototype=list(radius = 1,shape="Circle"))
setClass("Rectangle",contains="Shape",slots=list(edges="numeric"),prototype=list(edges=c(1,1),shape="Rectangle"))
setClass("Square",contains="Rectangle",slots=list(edges="numeric"),prototype=list(edges=1,shape="Square"))

setGeneric("getShape",function(obj,...) standardGeneric("getShape"))
setMethod("getShape","Shape",function(obj,...){
  cat(obj@shape,"\n")
})


setGeneric("area",function(obj,...) standardGeneric("area"))
setMethod("area","Ellipse",function(obj,...){
  cat("Ellipse Area :\n")
  pi * prod(obj@radius)
})
setMethod("area","Circle",function(obj,...){
  cat("Circle Area :\n")
  pi*obj@radius^2
})
setMethod("area","Rectangle",function(obj,...){
  cat("Rectangle Area :\n")
  prod(obj@edges)
})
setMethod("area","Square",function(obj,...){
  cat("Square Area :\n")
  obj@edges^2
})


setGeneric("circum",function(obj,...) standardGeneric("circum"))
setMethod("circum","Ellipse",function(obj,...){
  cat("Ellipse Circum :\n")
  2*pi*sqrt((obj@radius[1]^2+obj@radius[2]^2)/2)
})
setMethod("circum","Circle",function(obj,...){
  cat("Circle Circum :\n")
  2*pi*obj@radius
})
setMethod("circum","Rectangle",function(obj,...){
  cat("Rectangle Circum :\n")
  2*sum(obj@edges)
})
setMethod("circum","Square",function(obj,...){
  cat("Square Circum :\n")
  4*obj@edges
})

e1<-new("Ellipse",name="e1",radius=c(2,5))
c1<-new("Circle",name="c1",radius=2)

r1<-new("Rectangle",name="r1",edges=c(2,5))
s1<-new("Square",name="s1",edges=2)

area(e1)
area(c1)
circum(e1)
circum(c1)

area(r1)
area(s1)
circum(r1)
circum(s1)
```

通过这个例子，我们全面地了解了R语言中面向对象的使用，和S4对象系统的面向对象程序设计！

在程序员的世界里，世间万物都可以抽象成对象。

#### 转载请注明出处：http://blog.fens.me/r-class-s4/