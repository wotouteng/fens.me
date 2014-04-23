R语言基于RC的面向对象编程
===========

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-class-rc/

![R语言基于RC的面向对象编程](http://blog.fens.me/wp-content/uploads/2014/04/r-oo-rc.png)

#### 前言

本文接上一篇文章 [R语言基于S4的面向对象编程](http://blog.fens.me/r-class-s4/)，本文继续介绍R语言基于RC的面向对象编程。

RC对象系统从底层上改变了原有S3和S4对象系统的设计，去掉了泛型函数，真正地以类为基础实现面向对象的特征。

#### 目录

1. RC对象系统介绍
2. 创建RC类和对象
3. 对象赋值
4. 定义对象的方法
5. RC对象内置方法
6. RC类的辅助构造函数
7. RC对象系统的使用

## 1 RC对象系统介绍

RC是Reference classes的简称，在R语言的2.12版本被引入的，是最新一代的面向对象系统。

RC不同于原来的S3和S4对象系统，RC对象系统的方法是在类中自定的，而不是泛型函数。RC对象的行为更相似于其他的编程语言，实例化对象的语法也有所改变。

从面向对象的角度来说，我们下面要重定义几个名词。

+ 类：面向对象系统的基本类型，类是静态结构定义。
+ 对象：类实例化后，在内存中生成结构体。
+ 方法：是类中的函数定义，不通过泛型函数实现。

## 2 创建RC类和对象

本文的系统环境

* Linux: Ubuntu Server 12.04.2 LTS 64bit
* R: 3.0.1 x86_64-pc-linux-gnu

为了方便我们检查对象的类型，引入pryr包作为辅助工具。关于pryr包的介绍，请参考文章:[撬动R内核的高级工具包pryr](http://blog.fens.me/r-pryr/)

```{bash}
# 加载pryr包
> library(pryr)
```

### 2.1 如何创建RC类？

RC对象系统是以类为基本类型， 有专门的类的定义函数 setRefClass() 和 实例化则通过类的方法生成，我们一下如何用RC对象系统创建一个类。

2.1.1 setRefClass()

查看setRefClass的函数定义。

```{bash}
setRefClass(Class, fields = , contains = , methods =, where =, ...)
```

参数列表：

+ Class: 定义类名
+ fields: 定义属性和属性类型
+ contains: 定义父类，继承关系
+ methods: 定义类中的方法
+ where: 定义存储空间

从setRefClass()函数的定义来看，参数比S4的setClass()函数变少了。

### 2.2 创建RC类和实例

```{bash}
# 定义一个S4类
> User<-setRefClass("User",fields=list(name="character"))

# 查看User的定义
> User
Generator for class "User":

Class fields:
Name:       name
Class: character

Class Methods:
"callSuper", "copy", "export", "field", "getClass",
"getRefClass", "import", "initFields", "show", "trace",
"untrace", "usingMethods"

Reference Superclasses:
"envRefClass"


# 实例化一个User对象u1
> u1<-User$new(name="u1")

# 查看u1对象
> u1
Reference class object of class "User"
Field "name":
[1] "u1"

# 检查User类的类型
> class(User)
[1] "refObjectGenerator"
attr(,"package")
[1] "methods"
> is.object(User)
[1] TRUE
> otype(User)
[1] "RC"

# 检查u1的类型
> class(u1)
[1] "User"
attr(,"package")
[1] ".GlobalEnv"
> is.object(u1)
[1] TRUE
> otype(u1)
[1] "RC"
```

### 2.3 创建一个有继承关系的RC

```{bash}
# 创建RC类User
> User<-setRefClass("User",fields=list(name="character"))

# 创建User的子类Member
> Member<-setRefClass("Member",contains="User",fields=list(manager="User"))

# 实例化User
> manager<-User$new(name="manager")

# 实例化一个Son对象
> member<-Member$new(name="member",manager=manager)

# 查看member对象
> member
Reference class object of class "Member"
Field "name":
[1] "member"
Field "manager":
Reference class object of class "User"
Field "name":
[1] "manager"

# 查看member对象的name属性
> member$name
[1] "member

# 查看member对象的manager属性
> member$manager
Reference class object of class "User"
Field "name":
[1] "manager"

# 检查对象的属性类型
> otype(member$name)
[1] "primitive"
> otype(member$manager)
[1] "RC"
```

### 2.4 RC对象的默认值

RC的类有一个指定构造器方法 $initialize()，这个构造器方法在实例化对象时，会自动被运行一次，通过这个构造方法可以设置属性的默认值。

```{bash}
# 定义一个RC类
> User<-setRefClass("User",
+
+     # 定义2个属性
+     fields=list(name="character",level='numeric'),
+     methods=list(
+
+          # 构造方法
+          initialize = function(name,level){
+              print("User::initialize")
+
+              # 给属性增加默认值
+              name <<- 'conan'
+              level <<- 1
+           }
+      )
+ )

# 实例化对象u1
> u1<-User$new()
[1] "User::initialize"

# 查看对象u1，属性被增加了默认值
> u1
Reference class object of class "User"
Field "name":
[1] "conan"
Field "level":
[1] 1
```


## 3 对象赋值

```{bash}
# 定义User类
> User<-setRefClass("User",fields=list(name="character",age="numeric",gender="factor"))

# 定义一个factor类型
> genderFactor<-factor(c('F','M'))

# 实例化u1
> u1<-User$new(name="u1",age=44,gender=genderFactor[1])

# 查看age属性值
> u1$age
[1] 44
```

给u1的age属性赋值。

```{bash}
# 重新赋值
> u1$age<-10

# age属性改变
> u1$age
[1] 10
```

把u1对象赋值给u2对象。
```{bash}
# 把u1赋值给u2对象
> u2<-u1

# 查看u2的age属性
> u2$age
[1] 10

# 重新赋值
> u1$age<-20

# 查看u1,u2的age属性，都发生改变
> u1$age
[1] 20
> u2$age
[1] 20
```

这是由于把u1赋值给u2的时候，传递的是u1的实例化对象的引入，而不是值本身。这一点与其他语言中对象赋值是一样的。

如果想进行赋值而不是引入传递，可以用下面的方法实现。

```{bash}
# 调用u1的内置方法copy()，赋值给u3
> u3<-u1$copy()

# 查看u3的age属性
> u3$age
[1] 20

# 重新赋值
> u1$age<-30

# 查看u1的age属性，发生改变
> u1$age
[1] 30

# 查看u3的age属性，没有改变
> u3$age
[1] 20
```

对引入关系把握，可以减少值传递过程中的内存复制过程，可以让我们的程序运行效率更高。


## 4 定义对象的方法

在S3,S4的对象系统中，我们实现对象的行为时，都是借助于泛型函数来实现的。这种现实方法的最大问题是，在定义时函数和对象的代码是分离的，需要在运行时，通过判断对象的类型完成方法调用。而在RC对象系统中，方法可以定义在类的内部，通过实例化的对象完成方法调用。

```{bash}
# 定义一个RC类，包括方法
> User<-setRefClass("User",
+       fields=list(name="character",favorite="vector"),
+
+       # 方法属性
+       methods = list(
+
+           # 增加一个兴趣
+           addFavorite = function(x) {
+                 favorite <<- c(favorite,x)
+           },
+
+           # 删除一个兴趣
+           delFavorite = function(x) {
+                 favorite <<- favorite[-which(favorite == x)]
+           },
+
+           # 重新定义兴趣列表
+           setFavorite = function(x) {
+                 favorite <<- x
+           }
+       )
+ )

# 实例化对象u1
> u1<-User$new(name="u1",favorite=c('movie','football'))

# 查看u1对象
> u1
Reference class object of class "User"
Field "name":
[1] "u1"
Field "favorite":
[1] "movie"    "football"
```

接下来，进行方法操作。

```{bash}
# 删除一个兴趣
> u1$delFavorite('football')

# 查看兴趣属性
> u1$favorite
[1] "movie"

# 增加一个兴趣
> u1$addFavorite('shopping')
> u1$favorite
[1] "movie"    "shopping"

# 重置兴趣列表
> u1$setFavorite('reading')
> u1$favorite
[1] "reading"
```

直接到方法定义到类的内部，通过实例化的对象进行访问。这样就做到了，在定义时就能保证了方法的作用域，减少运行时检查的系统开销。

## 5 RC对象内置方法和内置属性

对于RC的实例化对象，除了我们自己定义的方法函数，还有几个内置的方法。之前属性复制赋值时使用的copy()方法，就是其中之一。

### 5.1 内置方法：

+ initialize 类的初始化函数，用于设置属性的默认值，只能在类定义的方法中使用。
+ callSuper 调用父类的同名方法，只能在类定义的方法中使用。
+ copy 复制实例化对象的所有属性。
+ initFields 给对象的属性赋值。
+ field 查看属性或给属性赋值。
+ getClass 查看对象的类定义。
+ getRefClass 同getClass。
+ show 查看当前对象。
+ export 查看属性值以类为作用域。
+ import 把一个对象的属性值赋值给另一个对象。
+ trace 跟踪对象中方法调用，用于程序debug。
+ untrace 取消跟踪。
+ usingMethods 用于实现方法调用，只能在类定义的方法中使用。这个方法不利于程序的健壮性，所以不建议使用。


接下来，我们使用这些内置的方法。

首先定义一个父类User，包括 name和level两个属性， addLevel和addHighLevel两个功能方法，initialize构造方法。

```{bash}
# 类User
> User<-setRefClass("User",
+    fields=list(name="character",level='numeric'),
+    methods=list(
+      initialize = function(name,level){
+        print("User::initialize")
+        name <<- 'conan'
+        level <<- 1
+      },
+      addLevel = function(x) {
+        print('User::addLevel')
+        level <<- level+x
+      },
+      addHighLevel = function(){
+        print('User::addHighLevel')
+        addLevel(2)
+      }
+    )
+)
```

定义子类Member，继承父类User，并包括同名方法addLevel覆盖父类的方法，在addLevel方法中，会调用父类的同名方法。

```{bash}
# 子类Member
> Member<-setRefClass("Member",contains="User",
+
+    # 子类中的属性
+    fields=list(age='numeric'),
+    methods=list(
+
+      # 覆盖父类的同名方法
+      addLevel = function(x) {
+          print('Member::addLevel')
+
+          # 调用父类的同名方法
+          callSuper(x)
+          level <<- level+1
+      }
+    )
+)
```

分别实例化对象u1,m1。
```{bash}
# 实例化u1
> u1<-User$new(name='u1',level=10)
[1] "User::initialize"

# 查看u1对象，$new()不能实现赋值的操作
> u1
Reference class object of class "User"
Field "name":
[1] "conan"
Field "level":
[1] 1

# 通过$initFields()向属性赋值
> u1$initFields(name='u1',level=10)
Reference class object of class "User"
Field "name":
[1] "u1"
Field "level":
[1] 10

# 实例化m1
> m1<-Member$new()
[1] "User::initialize"

> m1$initFields(name='m1',level=100,age=12)
Reference class object of class "Member"
Field "name":
[1] "m1"
Field "level":
[1] 100
Field "age":
[1] 12
```

执行$copy()方法，复制对象属性并传值。
```{bash}
# 属性复制到u2
> u2<-u1$copy()
[1] "User::initialize"

# 执行方法addLevel()，让level加1，u1已改变
> u1$addLevel(1);u1
[1] "User::addLevel"
Reference class object of class "User"
Field "name":
[1] "u1"
Field "level":
[1] 11

# u2的level与u1没有引入关系，u2没有变化
> u2
Reference class object of class "User"
Field "name":
[1] "u1"
Field "level":
[1] 10
```

使用方法field()，查看并给level属性赋值。
```{bash}
# 查看level属性值
> u1$field('level')
[1] 11

# 给level赋值为1
> u1$field('level',1)

# 查看level属性值
> u1$level
[1] 1
```

使用getRefClass()和getClass()方法查看u1对象的类定义。
```{bash}
# 类引入的定义
> m1$getRefClass()
Generator for class "Member":

Class fields:
Name:       name     level       age
Class: character   numeric   numeric

Class Methods:
"addHighLevel", "addLevel", "addLevel#User", "callSuper", "copy", "export", "field", "getClass", "getRefClass", "import", "initFields",
"initialize", "show", "trace", "untrace", "usingMethods"

Reference Superclasses:
"User", "envRefClass"

# 类定义
> m1$getClass()
Reference Class "Member":

Class fields:
Name:       name     level       age
Class: character   numeric   numeric

Class Methods:
"addHighLevel", "addLevel", "addLevel#User", "callSuper", "copy", "export", "field", "getClass", "getRefClass", "import", "initFields",
"initialize", "show", "trace", "untrace", "usingMethods"

Reference Superclasses:
"User", "envRefClass"

# 通过otype查看类型的不同
> otype(m1$getRefClass())
[1] "RC"
> otype(m1$getClass())
[1] "S4"
```

使用$show()方法查看对象属性值，$show()，同show()函数，对象直接输出时就是调用了$show()方法。
```{bash}
> m1$show()
Reference class object of class "Member"
Field "name":
[1] "m1"
Field "level":
[1] 100
Field "age":
[1] 12

> show(m1)
Reference class object of class "Member"
Field "name":
[1] "m1"
Field "level":
[1] 100
Field "age":
[1] 12

> m1
Reference class object of class "Member"
Field "name":
[1] "m1"
Field "level":
[1] 100
Field "age":
[1] 12
```

使用 $trace()跟踪方法调用 ，再用 $untrace()方法取消跟踪绑定。
```{bash}
# 对addLevel()方法跟踪
> m1$trace("addLevel")
Tracing reference method "addLevel" for object from class "Member"
[1] "addLevel"

# 调用addLevel()方法，Tracing m1$addLevel(1)被打印，跟踪生效
> m1$addLevel(1)
Tracing m1$addLevel(1) on entry
[1] "Member::addLevel"
[1] "User::addLevel"

# 调用父类的addHighLevel()方法，Tracing addLevel(2)被打印，跟踪生效
> m1$addHighLevel()
[1] "User::addHighLevel"
Tracing addLevel(2) on entry
[1] "Member::addLevel"
[1] "User::addLevel"

# 取消对addLevel()方法跟踪
> m1$untrace("addLevel")
Untracing reference method "addLevel" for object from class "Member"
[1] "addLevel"
```


使用$export()方法，以类为作用域查看属性值。

```{bash}
# 查看在Member类中的属性
> m1$export('Member')
Reference class object of class "Member"
Field "name":
[1] "m1"
Field "level":
[1] 105
Field "age":
[1] 12

# 查看在User类中的属性，当前作用域不包括age属性。
> m1$export('User')
[1] "User::initialize"
Reference class object of class "User"
Field "name":
[1] "m1"
Field "level":
[1] 105
```

使用$import()方法，把一个对象的属性值赋值给另一个对象。
```{bash}
# 实例化m2
> m2<-Member$new()
[1] "User::initialize"
> m2
Reference class object of class "Member"
Field "name":
[1] "conan"
Field "level":
[1] 1
Field "age":
numeric(0)

# 把m1对象的值赋值给m2对象
> m2$import(m1)
> m2
Reference class object of class "Member"
Field "name":
[1] "m1"
Field "level":
[1] 105
Field "age":
[1] 12
```

### 5.2 内置属性：

RC对象实例化后，有两个内置属性：

+ .self 实例化对象自身
+ .refClassDef 类的定义类型

```{bash}
# $.self属性
> m1$.self
Reference class object of class "Member"
Field "name":
[1] "m1"
Field "level":
[1] 105
Field "age":
[1] 12

#  m1$.self和m1 完全相同
> identical(m1$.self,m1)
[1] TRUE

# 查看类型
> otype(m1$.self)
[1] "RC"


# $.refClassDef属性
> m1$.refClassDef
Reference Class "Member":

Class fields:
Name:       name     level       age
Class: character   numeric   numeric

Class Methods:
"addHighLevel", "addLevel", "addLevel#User", "callSuper", "copy", "export", "field", "getClass", "getRefClass", "import", "initFields",
"initialize", "show", "trace", "untrace", "usingMethods"

Reference Superclasses:
"User", "envRefClass"

# 与$getClass()相同
> identical(m1$.refClassDef,m1$getClass())
[1] TRUE

# 查看类型
> otype(m1$.refClassDef)
[1] "S4"
```

## 6  RC类的辅助函数

当定义好了RC的类结构，有一些辅助函数可以帮助我们查看类型的属性和方法，上面用于创建实例化的对象的$new()函数，也属性这类辅助函数。

+ new 用于实例化对象。
+ help 用于查询类中方法的调用。
+ methods 列出类中定义的所有方法。
+ fields 列出类中定义的所有属性。
+ lock 给属性加锁，实例化对象的属性只允许赋值一次，即赋值变量，不可修改。
+ trace 跟踪方法。
+ accessors 给属性生成get/set方法。

接下来，我们使用辅助函数，继续使用我们之前定义的User的类的结构。

```{bash}
# 定义User类
> User<-setRefClass("User",
+    fields=list(name="character",level='numeric'),
+    methods=list(
+      initialize = function(name,level){
+        print("User::initialize")
+        name <<- 'conan'
+        level <<- 1
+      },
+      addLevel = function(x) {
+        print('User::addLevel')
+        level <<- level+x
+      },
+      addHighLevel = function(){
+        print('User::addHighLevel')
+        addLevel(2)
+      }
+    )
+)

# 实例化对象u1
> u1<-User$new()

# 列出User类中的属性
> User$fields()
       name       level
"character"   "numeric"

# 列出User类中的方法
> User$methods()
 [1] "addHighLevel" "addLevel"     "callSuper"
 [4] "copy"         "export"       "field"
 [7] "getClass"     "getRefClass"  "import"
[10] "initFields"   "initialize"   "show"
[13] "trace"        "untrace"      "usingMethods"

# 查看User类的方法调用
> User$help("addLevel")
Call:
$addLevel(x)

> User$help("show")
Call:
$show()
```

给User类中的属性，增加get/set方法
```{bash}
# 给level属性增加get/set方法
> User$accessors("level")

# 给name属性增加get/set方法
> User$accessors("name")

# 列出所有方法
> User$methods()
 [1] "addHighLevel" "addLevel"     "callSuper"
 [4] "copy"         "export"       "field"
 [7] "getClass"     "getLevel"     "getName"
[10] "getRefClass"  "import"       "initFields"
[13] "initialize"   "setLevel"     "setName"
[16] "show"         "trace"        "untrace"
[19] "usingMethods"
```

使用$trace()函数，跟踪addLevel方法
```{bash}
# 跟踪User类的addLevel方法
> User$trace('addLevel')
Tracing reference method "addLevel" for class
"User"
[1] "addLevel"

# 实例化对象u3
> u3<-User$new(name='u3',level=1)
[1] "User::initialize"

# addLevel方法调用，出发跟踪日志 Tracing u3$addLevel(2)
> u3$addLevel(2)
Tracing u3$addLevel(2) on entry
[1] "User::addLevel"
```

使用$lock()函数，把level属性设置为常量。
```{bash}
# 锁定level属性
> User$lock("level")

# 查看User类中被锁定的属性
> User$lock()
[1] "level"

# 实例化对象u3，这时level属性已经被赋值一次
> u3<-User$new()
[1] "User::initialize"
> u3
Reference class object of class "User"
Field "name":
[1] "conan"
Field "level":
[1] 1

# 给level属性，再次赋值出错
> u3$level=1
Error: invalid replacement: reference class field ‘level’ is read-only
> u3$addLevel(2)
[1] "User::addLevel"
Error: invalid replacement: reference class field ‘level’ is read-only
```

## 7 RC对象系统的使用

我们接下用RC对象系统做一个例子，定义一套动物叫声研究模型。

### 7.1 任务一：定义动物的数据结构和发声方法。

假设最Animal为动物的基类，包括 研究的动物包括 猫(cat)、狗(dog)、鸭(duck)。

+ 定义动物的数据结构
+ 分别定义3种动物的发声bark()方法

如图所示结构：

![](http://blog.fens.me/wp-content/uploads/2014/04/animal.png)


定义动物的数据结构，包括基类的结构 和 3种动物的结构。

```{bash}
# 创建Animal类，包括name属性，构造方法initialize()，叫声方法bark()。
> Animal<-setRefClass("Animal",
+  fields=list(name="character"),
+  methods=list(
+    initialize = function(name) name <<- 'Animal',
+    bark = function() print("Animal::bark")
+  )
+)

# 创建Cat类，继承Animal类，并重写(Overwrite)了 initialize() 和 bark()。
> Cat<-setRefClass("Cat",contains="Animal",
+  methods=list(
+    initialize = function(name) name <<- 'cat',
+    bark = function() print(paste(name,"is miao miao"))
+  )
+)

# 创建Dog类，继承Animal类，并重写(Overwrite)了 initialize() 和 bark()。
> Dog<-setRefClass("Dog",contains="Animal",
+  methods=list(
+    initialize = function(name) name <<- 'dog',
+    bark = function() print(paste(name,"is wang wang"))
+  )
+)

# 创建Duck类，继承Animal类，并重写(Overwrite)了 initialize() 和 bark()。
> Duck<-setRefClass("Duck",contains="Animal",
+   methods=list(
+     initialize = function(name) name <<- 'duck',
+     bark = function() print(paste(name,"is ga ga"))
+   )
+)
```

接下来，我们实例化对象，然后研究它们的叫声。

```{bash}
# 创建cat实例
> cat<-Cat$new()
> cat$name
[1] "cat"

# cat叫声
> cat$bark()
[1] "cat is miao miao"

# 创建dog实例，并给dog起名叫Huang
> dog<-Dog$new()
> dog$initFields(name='Huang')
Reference class object of class "Dog"
Field "name":
[1] "Huang"
> dog$name
[1] "Huang"

# dog叫声
> dog$bark()
[1] "Huang is wang wang"

# 创建duck实例
> duck<-Duck$new()

# duck叫声
> duck$bark()
[1] "duck is ga ga"
```

### 7.2 任务二：定义动物的体貌特征

动物的体貌特征，包括 头、身体、肢、翅等，我在这里只定义肢和翅的特征。

3种动物都有肢，cat和dog是四肢，duck是二肢和二翅。

如图所示结构：

![](http://blog.fens.me/wp-content/uploads/2014/04/animal2.png)

我们需要对原结构进行修改。

```{bash}
# 定义Animal类，增加limbs属性，默认值为4
Animal<-setRefClass("Animal",
    fields=list(name="character",limbs='numeric'),
    methods=list(
      initialize = function(name) {
          name <<- 'Animal'
          limbs<<-4
      },
      bark = function() print("Animal::bark")
    )
)

# 在Cat类的 initialize()方法中，执行callSuper()方法，调用父类的同名方法
Cat<-setRefClass("Cat",contains="Animal",
     methods=list(
       initialize = function(name) {
         callSuper()
         name <<- 'cat'
       },
       bark = function() print(paste(name,"is miao miao"))
     )
)

# 在Dog类的 initialize()方法中，执行callSuper()方法，调用父类的同名方法
Dog<-setRefClass("Dog",contains="Animal",
     methods=list(
       initialize = function(name) {
         callSuper()
         name <<- 'dog'
       },
       bark = function() print(paste(name,"is wang wang"))
     )
)

# 在Dog类的定义wing属性， 并在initialize()方法，定义limbs和wing属性的默认值
Duck<-setRefClass("Duck",contains="Animal",
    fields=list(wing='numeric'),
    methods=list(
      initialize = function(name) {
          name <<- 'duck'
          limbs<<- 2
          wing<<- 2
      },
      bark = function() print(paste(name,"is ga ga"))
    )
)
```

实例化对象并查看3种动物的属性值。

```{bash}
# 实例化cat对象，属性limbs为4
> cat<-Cat$new();cat
Reference class object of class "Cat"
Field "name":
[1] "cat"
Field "limbs":
[1] 4

# 实例化dog对象，属性limbs为4
> dog<-Dog$new()
> dog$initFields(name='Huang')
Reference class object of class "Dog"
Field "name":
[1] "Huang"
Field "limbs":
[1] 4
> dog
Reference class object of class "Dog"
Field "name":
[1] "Huang"
Field "limbs":
[1] 4

# 实例化duck对象，属性limbs为2，wing为2
> duck<-Duck$new();duck
Reference class object of class "Duck"
Field "name":
[1] "duck"
Field "limbs":
[1] 2
Field "wing":
[1] 2
```


### 7.3 任务三：定义动物的行动方式。

对于 猫(cat)，狗(dog)，鸭(duck) 来说，它们都可以在陆地上行动，而且还有各自不同的行动方式。

特有行动方式：

+ 猫(cat) 爬树
+ 狗(dog) 游泳
+ 鸭(duck) 游泳，短距离飞行

如图所示结构：

![](http://blog.fens.me/wp-content/uploads/2014/04/animal3.png)

接下来，我们按动物的不同行动方式进行建模。

```{bash}
# 定义类Animal，增加action()方法，用于通用的行为陆地上行动。
> Animal<-setRefClass("Animal",
+    fields=list(name="character",limbs='numeric'),
+    methods=list(
+      initialize = function(name) {
+        name <<- 'Animal'
+        limbs<<-4
+      },
+      bark = function() print("Animal::bark"),
+      action = function() print("I can walk on the foot")
+    )
+)

# 定义Cat类，重写action()方法，并增加爬树的行动
> Cat<-setRefClass("Cat",contains="Animal",
+     methods=list(
+       initialize = function(name) {
+         callSuper()
+         name <<- 'cat'
+       },
+       bark = function() print(paste(name,"is miao miao")),
+       action = function() {
+         callSuper()
+         print("I can Climb a tree")
+       }
+     )
+  )

# 定义Dog类，重写action()方法，并增加游泳行动
> Dog<-setRefClass("Dog",contains="Animal",
+   methods=list(
+     initialize = function(name) {
+       callSuper()
+       name <<- 'dog'
+     },
+     bark = function() print(paste(name,"is wang wang")),
+     action = function() {
+         callSuper()
+         print("I can Swim.")
+     }
+   )
+)

# 定义Duck类，重写action()方法，并增加游泳和短距离飞行
> Duck<-setRefClass("Duck",contains="Animal",
+    fields=list(wing='numeric'),
+    methods=list(
+      initialize = function(name) {
+        name <<- 'duck'
+        limbs<<- 2
+        wing<<- 2
+      },
+      bark = function() print(paste(name,"is ga ga")),
+      action = function() {
+        callSuper()
+        print("I can swim.")
+        print("I also can fly a short way.")
+     }
+    )
+)
```

实例化对象，并运行action()方法。

cat的行动。
```{bash}
# 实例化cat
> cat<-Cat$new()

# cat的行动
> cat$action()
[1] "I can walk on the foot"
[1] "I can Climb a tree"
```

dog的行动。
```{bash}
> dog<-Dog$new()
> dog$action()
[1] "I can walk on the foot"
[1] "I can Swim."
```

duck的行动。
```{bash}
> duck<-Duck$new()
> duck$action()
[1] "I can walk on the foot"
[1] "I can swim."
[1] "I also can fly a short way."
```

通过这个例子，我们应该就能全面地了解了R语言中基于RC对象系统的面向对象程序设计了！RC对象系统提供了完全的面向对象的实现。

#### 转载请注明出处：http://blog.fens.me/r-class-rc/