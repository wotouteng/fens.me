R语言面向对象编程
===========

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-object-oriented-intro/

![R语言面向对象编程](http://blog.fens.me/wp-content/uploads/2014/04/R-OO.png)

#### 前言

面向对象是一种对现实世界理解和抽象的方法，当代码复杂度增加难以维护的时候，面向对象就会显得非常重要。我经历过Java和Javascript两种语言从面向过程到面向对象思路的改造，并感觉这种变化也会出现在R语言中。在工业界的引导下，R将走向大规则的企业应用，因此面向对象的编程方式将成为R语言的一种非常重要的发展方向，动起来迎接R的进步。

#### 目录

1. 什么是面向对象？
2. R为什么要进行面向对象编程?
3. R的面向对象编程
4. 与其他语言的对比

## 1 什么是面向对象？

面向对象是一种对现实世界理解和抽象的方法，是计算机编程技术发展到一定阶段后的产物。早期的计算机编程是基于面向过程的方法，例如实现算术运算2+3+4=9，通过设计一个算法就可以解决当时的问题。

随着计算机技术的不断提高，计算机被用于解决越来越复杂的问题。一切事物皆对象，通过面向对象的方式，将现实世界的事物抽象成对象，现实世界中的关系抽象成类、继承，帮助人们实现对现实世界的抽象与数字建模。通过面向对象的方法，更利于用人理解的方式对复杂系统进行分析、设计与编程。同时，面向对象能有效提高编程的效率，通过封装技术，消息机制可以像搭积木的一样快速开发出一个全新的系统。面向对象是指一种程序设计范型，同时也是一种程序开发的方法。对象指的是类的集合。它将对象作为程序的基本单元，将程序和数据封装其中，以提高软件的重用性、灵活性和扩展性。

面向对象的3个特征：封装，继承，多态

**封装**：是把客观事物封装成抽象的类，并且类可以把自己的数据和方法只让可信的类或者对象操作，对不可信的进行信息隐藏。

![封装](http://blog.fens.me/wp-content/uploads/2014/04/oo1.png)

我们通过面向对象的思想，定义老师和学生两个对象，并分别定义老师和学生的行为。

+ 老师的行为：讲课，布置作业，批作业
+ 学生的行为：听课，写作业，考试

通过封装就把两个客观事物进行了抽象，并设置了事情的行为。

**继承**：子类自动共享父类数据结构和方法的机制，这是类之间的一种关系。在定义和实现一个类的时候，可以在一个已经存在的类的基础之上来进行，使用现有类的所有功能，并在无需重新编写原来的类的情况下对这些功能进行扩展。通过继承创建的新类称为“子类”或“派生类”；被继承的类称为“基类”、“父类”或“超类”。

![继承](http://blog.fens.me/wp-content/uploads/2014/04/oo2.png)

通常每门课都会从学生中选出这门课的课代表，来帮助老师和其他同学的沟通。课代表会有一些比普通同学更多特权。通过继承关系，把普通同学和课代表区别为两个子类，课代表不仅有普通同学的行为，还有帮助老师批作业的行为。

**多态**: 指由继承而产生的相关的不同的类，其对象对同一消息会做出不同的响应。

![多态](http://blog.fens.me/wp-content/uploads/2014/04/oo3.png)

临近期末考试时，总有考的好的同学和考的不好的同学。所以，对于优等生来说，他的考试结果是优；次等生，考试结果就不是太好。相同行为对于由继承而产生的相关的不同的对象，结果是不同的。

所以，通过面向对象的思想，我们可以把客观世界的事物都进行抽象。

**is a 和 has a**

在客观世界中有若干类，这些类之间有一定的结构关系。通常有两种主要的结构关系，is a，和 has a。

+ is a: 为继承关系，比如 菱形、圆形和方形都是一种形状
+ has a：为组合关系或聚合关系，比如 电脑是由显示器、CPU、硬盘等组成

## 2 R为什么要进行面向对象编程?

R主要面向统计计算，而且代码量一般不会很大，几十行，几百行，使用面向过程的编程方法就可以很好地完成编程的任务。

不过，虽然R语言的持续手热，伴随着越来越多的工程背景的人的加入，R语言开始向更多的领域发展。原来的少量的代码的面向过程的编码方式，会越来越难以维护海量代码的项目，所以必须有一种新的编程方式来代码原来的面向过程的编码思路，这种新的编程方式就是面向对象编程(Object Oriented Programming, OOP)。

面向对象编程，早在C++/Java时代就被广泛使用了，几乎90%以上的Java框架都是按面向对象的方法设计的；8年前Javascript各种面向过程编码让前端开发困难重重，直到Google的Gmail的Web端出现，才让大家认识到原来Javascript也可以面向对象编程，随后的jQuery, ExtJS等类库的完全面向对象的实现，终于让Javascript承得起前端的天空，后来的Node的诞生更是让Javascript拓宽了应用领域。

当R语言被大家所看好的同时，我们也要开始思考，如何才能让R成为工业界的开发语言？应用如何构建非统计计算的项目？如何用R有效的编写10万行以上的代码？

我想这个答案就是以面向对象进行编程，现在的R就像8年前的Javascript，需要大公司和牛人来推动。从我的观察来看，以Hadley Wickham为代表的R语言领军人物，已经开始在R包中全面引入面向对象思路进行R包的开发了。以面向对象思想开发的R包memoise，请参考文章：R语言本地缓存memoise

## 3 R的面向对象编程

R的面向对象编程是基于泛型函数(generic function)的，而不是基于类层次结构。接下来，我从面向对象的3个特征入手，分别用R语言进行实现，使用的案例为上文中，老师和学生的3幅图。

### 3.1 R语言实现封装

```{bash}
# 定义老师对象和行为
> teacher <- function(x, ...) UseMethod("teacher")
> teacher.lecture <- function(x) print("讲课")
> teacher.assignment <- function(x) print("布置作业")
> teacher.correcting <- function(x) print("批改作业")
> teacher.default<-function(x) print("你不是teacher")

# 定义同学对象和行为
> student <- function(x, ...) UseMethod("student")
> student.attend <- function(x) print("听课")
> student.homework <- function(x) print("写作业")
> student.exam <- function(x) print("考试")
> student.default<-function(x) print("你不是student")

# 定义两个变量，a老师和b同学
> a<-'teacher'
> b<-'student'

# 给老师变量设置行为
> attr(a,'class') <- 'lecture'
# 执行老师的行为
> teacher(a)
[1] "讲课"

# 给同学变量设置行为
> attr(b,'class') <- 'attend'
# 执行同学的行为
> student(b)
[1] "听课"

> attr(a,'class') <- 'assignment'
> teacher(a)
[1] "布置作业"

> attr(b,'class') <- 'homework'
> student(b)
[1] "写作业"

> attr(a,'class') <- 'correcting'
> teacher(a)
[1] "批改作业"

> attr(b,'class') <- 'exam'
> student(b)
[1] "考试"

# 定义一个变量，既是老师又是同学
> ab<-'student_teacher'
# 分别设置不同对象的行为
> attr(ab,'class') <- c('lecture','homework')
# 执行老师的行为
> teacher(ab)
[1] "讲课"
# 执行同学的行为
> student(ab)
[1] "写作业"
```

### 3.2 R语言实现继承

```{bash}
# 给同学对象增加新的行为
> student.correcting <- function(x) print("帮助老师批改作业")

# 辅助变量用于设置初始值
> char0 = character(0)

# 实现继承关系
> create <- function(classes=char0, parents=char0) {
+     mro <- c(classes)
+     for (name in parents) {
+         mro <- c(mro, name)
+         ancestors <- attr(get(name),'type')
+         mro <- c(mro, ancestors[ancestors != name])
+     }
+     return(mro)
+ }

# 定义构造函数，创建对象
> NewInstance <- function(value=0, classes=char0, parents=char0) {
+     obj <- value
+     attr(obj,'type') <- create(classes, parents)
+     attr(obj,'class') <- c('homework','correcting','exam')
+     return(obj)
+ }

# 创建父对象实例
> StudentObj <- NewInstance()

# 创建子对象实例
> s1 <- NewInstance('普通同学',classes='normal', parents='StudentObj')
> s2 <- NewInstance('课代表',classes='leader', parents='StudentObj')

# 给课代表，增加批改作业的行为
> attr(s2,'class') <- c(attr(s2,'class'),'correcting')

# 查看普通同学的对象实例
> s1
[1] "普通同学"
attr(,"type")
[1] "normal"     "StudentObj"
attr(,"class")
[1] "homework"   "attend" "exam"

# 查看课代表的对象实例
> s2
[1] "课代表"
attr(,"type")
[1] "leader"     "StudentObj"
attr(,"class")
[1] "homework"   "attend" "exam"       "correcting"
```

### 3.3 R语言实现多态

```{bash}
# 创建优等生和次等生，两个实例
> e1 <- NewInstance('优等生',classes='excellent', parents='StudentObj')
> e2 <- NewInstance('次等生',classes='poor', parents='StudentObj')

# 修改同学考试的行为，大于85分结果为优秀，小于70分结果为及格
> student.exam <- function(x,score) {
+     p<-"考试"
+     if(score>85) print(paste(p,"优秀",sep=""))
+     if(score<70) print(paste(p,"及格",sep=""))
+ }

# 执行优等生的考试行为，并输入分数为90
> attr(e1,'class') <- 'exam'
> student(e1,90)
[1] "考试优秀"

# 执行次等生的考试行为，并输入分数为66
> attr(e2,'class') <- 'exam'
> student(e2,66)
[1] "考试及格"
```

这样通过R语言的泛型函数，我们就实现了面向对象的编程。

## 4 R的面向过程编程

接下来，我们再次对比用R语言用面向过程实现上面的逻辑。

### 4.1 定义老师和同学两个对象和行为

```{bash}
# 辅助变量用于设置初始值
> char0 = character(1)

# 定义老师对象和行为
> teacher_fun<-function(x=char0){
+     if(x=='lecture'){
+         print("讲课")
+     }else if(x=='assignment'){
+         print("布置作业")
+     }else if(x=='correcting'){
+         print("批改作业")
+     }else{
+         print("你不是teacher")
+     }
+ }

# 定义同学对象和行为
> student_fun<-function(x=char0){
+     if(x=='attend'){
+         print("听课")
+     }else if(x=='homework'){
+         print("写作业")
+     }else if(x=='exam'){
+         print("考试")
+     }else{
+         print("你不是student")
+     }
+ }

# 执行老师的一个行为
> teacher_fun('lecture')
[1] "讲课"

# 执行同学的一个行为
> student_fun('attend')
[1] "听课"
```

### 4.2 区别普通同学和课代表的行为

```{bash}
# 重定义同学的函数，增加角色判断
> student_fun<-function(x=char0,role=0){
+     if(x=='attend'){
+         print("听课")
+     }else if(x=='homework'){
+         print("写作业")
+     }else if(x=='exam'){
+         print("考试")
+     }else if(x=='correcting'){
+         if(role==1){#课代表
+             print("帮助老师批改作业")
+         }else{
+             print("你不是课代表")
+         }
+     }else{
+         print("你不是student")
+     }
+ }

# 以普通同学的角色，执行课代表的行为
> student_fun('correcting')
[1] "你不是课代表"

# 以课代表的角色，执行课代表的行为
> student_fun('correcting',1)
[1] "帮助老师批改作业"
```

我在修改student_fun()函数的同时，已经增加了原函数的复杂度。

### 4.3 参加考试，以成绩区别出优等生和次等生

```{bash}
# 修改同学的函数定义，增加考试成绩参数
> student_fun<-function(x=char0,role=0,score){
+     if(x=='attend'){
+         print("听课")
+     }else if(x=='homework'){
+         print("写作业")
+     }else if(x=='exam'){
+         p<-"考试"
+         if(score>85) print(paste(p,"优秀",sep=""))
+         if(score<70) print(paste(p,"及格",sep=""))
+     }else if(x=='correcting'){
+         if(role==1){#课代表
+             print("帮助老师批改作业")
+         }else{
+             print("你不是课代表")
+         }
+     }else{
+         print("你不是student")
+     }
+ }

# 执行考试函数，考试成绩为大于85分，为优等生
> student_fun('exam',score=90)
[1] "考试优秀"

# 执行考试函数，考试成绩为小于70分，为次等生
> student_fun('exam',score=66)
[1] "考试及格"
```

我再一次用面向过程的代码，实现了整个的编辑逻辑。再用到面向过程来写程序的时候，每一次的需求变化，都需要对原始代码进行修改，从而不仅增加了复杂度，而且不利于长久的维护。更多思考留给了大家！

本文抛砖引玉地讲了R语言的面向对象的编程，其中部分代码有些不够严谨，本文只希望给大家思路上的认识，更具体的面向对象编程实例，会在以后的文章中进行讨论。

#### 转载请注明出处：http://blog.fens.me/r-object-oriented-intro/