RHadoop培训 之 Java基础课
============

[RHadoop实践系列文章](http://blog.fens.me/series-rhadoop/)，包含了R语言与Hadoop结合进行海量数据分析。Hadoop主要用来存储海量数据，R语言完成MapReduce 算法，用来替代Java的MapReduce实现。有了RHadoop可以让广大的R语言爱好者，有更强大的工具处理大数据1G, 10G, 100G, TB, PB。 由于大数据所带来的单机性能问题，可能会一去不复返了。

RHadoop实践是一套系列文章，主要包括[”Hadoop环境搭建”](http://blog.fens.me/rhadoop-hadoop/)，[”RHadoop安装与使用”](http://blog.fens.me/rhadoop-rhadoop/)，[”R实现MapReduce的协同过滤算法”](http://blog.fens.me/rhadoop-mapreduce-rmr/)，[”HBase和rhbase的安装与使用”](http://blog.fens.me/rhadoop-hbase-rhase/)。对于单独的R语言爱好者，Java爱好者，或者Hadoop爱好者来说，同时具备三种语言知识并不容 易。此文虽为入门文章，但R,Java,Hadoop基础知识还是需要大家提前掌握。

####关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

####转载请注明出处：  
http://blog.fens.me/rhadoop-linux-basic/

![](http://blog.fens.me/wp-content/uploads/2013/07/java-title.jpg)

####前言

覆盖java基础知识，快速上手，RHadoop环境的搭建基础课。

####目录

1. 背景知识  
2. 开发环境
3. JAVA的编译及运行环境
4. JAVA语法
5. JDK基本包介绍
6. JAVA项目(ant, maven)

##1. 背景知识

###Java起源

Java是由Sun Microsystems公司于 1995年5月推出的Java面向对象程序设计语言（以下简称Java语言）和Java平台的总称。由James Gosling和同事们共同研发，并在1995年正式推出。用Java实现的HotJava浏览器（支持Java applet）显示了Java的魅力：跨平台、动态的Web、Internet计算。从此，Java被广泛接受并推动了Web的迅速发展，常用的浏览器均支持Javaapplet。另一方面，Java技术也不断更新。(2010年Oracle公司收购了SUN)

###Java组成

Java由四方面组成：Java编程语言、Java类文件格式、Java虚拟机和Java应用程序接口平台(Java API)。

Java应用编程接口为Java应用提供了一个独立于操作系统的标准接口，可分为基本部分和扩展部分。在硬件或操作系统平台上安装一个Java平台之后，Java应用程序就可运行。Java平台已经嵌入了几乎所有的操作系统。这样Java程序可以只编译一次，就可以在各种系统中运行。常用的Java平台基于Java1.5，最近版本为Java8.0。

###Java体系

JavaSE，standard edition，标准版，是我们通常用的一个版本.

JavaEE，enterprise edition，企业版，使用这种JDK开发JavaEE应用程序.

JavaME，micro edition，主要用于移动设备、嵌入式设备上的java应用程序.

###Java版本

JavaSE Platform JDK6:

![](http://blog.fens.me/wp-content/uploads/2013/07/jdk-arch.png)

下载JDK6. http://www.oracle.com/technetwork/java/javasebusiness/downloads/java-archive-downloads-javase6-419409.html

![](http://blog.fens.me/wp-content/uploads/2013/07/jdk16.png)

##2. 开发环境

EclipseIDE集成开发环境

http://www.eclipse.org/downloads/

![](http://blog.fens.me/wp-content/uploads/2013/07/eclipse.png)

##3. JAVA的编译及运行环境(java,javac)

###运行原理：

先编译： *.java文件 => *.class文件(字节码)

运行： *.classs => JVM（JAVA虚拟机）

![](http://blog.fens.me/wp-content/uploads/2013/07/java-compile.png)

##4. JAVA语法

###1). 命名标识符

必须以字母、美元符号或下划线开头。数字不能开头

第一个字符之后可以是任意长度的包含数字、字母、美元符号、下划线的任意组合。

不能使用Java关键字和保留字做标识符

大小写敏感的，Unicode字符会被视为普通字母对待。

A1.java, 1A.java, _a1.java, $_a1.java

###2). 关键字

JavaSE6一共有50个关键字:

```{bash}
  abstract  continue	for	new	switch
    assert	default	goto	package	synchronized
    boolean	do	if	private	this
    break	double	implements	protected	throw
    byte	else	import	public	throws
    case	enum	instanceof	return	transient
    catch	extentds	int	short	try
    char	final	interface	static	void
    class	finally	long	strictfp	volatile
    const	float	native	super	while
```

####类和接口:

+ class:声明类
+ interface：声明接口
+ abstract：声明抽象类
+ implements：实现接口
+ extentds: 类继承
+ import：包导入
+ package：声明属于哪一个包
+ static：静态属性，一个类下所有对象共享的属性。
+ throws：声明异常
+ void：空对象

####基本数据类型

+ int：整型
+ long：长整型
+ short：短整型
+ float：单精度浮点型
+ double：双精度浮点型
+ boolean：布尔值true和false
+ byte：字节型
+ char：字符型
+ enum：枚举类型

####流程控制

+ if：条件语句
+ else：条件分支
+ for:循环语句
+ while:循环语句
+ do和while：循环语句
+ break:跳出循环语句
+ continue:只跳出本次循环，还要继续执行下一次的循环
+ return：返回
+ switch：多条件语句
+ case：多条件分支
+ default：默认值

####访问范围

+ private：声明私有类型，只能供当前类内部访问
+ 默认属性：不加任何访问权限修饰符，可以被同包的类访问
+ protected：声明保护类型，可以被同包的类和不同包的子类访问
+ public：声明公共类型，可以供所有其它类访问

####类的实例

+ new：创建一个新的对象实例
+ this：引用相前对象实例
+ super：引用超类对象实例

####异常处理

+ try：开始捕获错误的语句
+ catch：捕获错误的语句
+ finally：无论有没有异常发生都执行代码

####很少会用到的

+ native是方法修饰符。调用其他语言的JNI接口
+ transient：变量修饰符。对象存储时，变量状态不会被持久化
+ volatile：修饰变量。在每次被线程访问时，都强迫从共享内存中重读该成员变量的值。而且，当成员变量发生变化时，强迫线程将变化值回写到共享内存
+ strictfp修饰类和方法，意思是FP-strict，精确浮点，符合IEEE-754规范的。当一个class或interface用strictfp声明，内部所有的float和double表达式都会成为strictfp的。接口的method不能被声明为strictfp的，class的可以
+ goto:被保留的关键字

###3). 变量

所谓变量，就是值可以被改变的量。

```{bash}
public class Variable{
    public static void main(String[] args){
        String myName ="nabula";
        myName = "nebulayao";
        System.out.println(myName);
    }
}
```

###4). 常量

所谓常量，值不允许改变的量。要声明一个常量用final 修饰，常量按照Java命名规范需要用全部大写，单词之间用下划线隔开：

```{bash}
// 游戏方向设定 北 南 东 西
final int NORTH = 1;
final int SOUTH = 2;
final int EAST = 3;
final int WEST = 4;
```

###5). 数据类型

+ 分为基本数据类型(Primitive Type)
+ 引用数据类型(Reference Data Type)

![](http://blog.fens.me/wp-content/uploads/2013/07/datatype.jpg)

###6). 赋值

“=” 是个赋值运算符，都是把右侧的值赋予左侧的变量。

```{bash}
public class Assignment{
    public static void main(String[] args){
        int a=1;
        int b=1,c=1;
        int d=a=b=c=2;
        System.out.println("a="+a+" b="+b +" c="+c+" d="+d);
    }
}
```

###7). 注释

程序中的注释是程序设计者与程序阅读者之间沟通的手段，是写给程序员看的代码。通常情况下编译器会忽略注释部分，不做语法检查。

Java中的注释有三种：

+ // 注释一行
+ /* 注释若干行 */
+ /**注释若干行，并写入 javadoc 文档 */

###8). 运算符

####算术运算符

+(加), –(减), *(乘), /(除), %(求余), ++(递增), –-(递减)

####逻辑运算符

逻辑运算符只对布尔型操作数进行运算并返回一个布尔型数据。

6个逻辑运算符：&& , || ,& , | ,！, ^

####位运算符

位运算符是对整数操作数以二进制的每一位进行操作，返回结果也是一个整数。

逻辑位运算符：按位取反~, 按位与&, 按位或 |, 按位异或 ^,移位运算符有 左移<< , 右移>>

####赋值运算符

```{bash}
运算符     举例       等效表达式
  +=      a += b      a = a+b
  -=      a -= b      a = a-b
  *=      a *= b      a = a*b
  /=      a /= b      a = a/b
  %=      a %= b      a = a%b
  &=      a &= b      a = a&b
  |=      a |= b      a = a|b
  ^=      a ^= b      a = a^b
  <<=     a <<= b     a = a<<b
  >>=     a >>= b     a = a>>b
  >>>=    a >>>= b    a = a>>>b
```

####关系运算符

```{bash}
运算符     功能       举例         运算结果        可运算类型
>         大于        'a'>'b'       false         整数、浮点数、字符
<         小于        2<3.0         true          整数、浮点数、字符
==        等于      'x'==88     true            任意
!=        不等于     true!=true    false           任意
>=        大于等于    6.6>=8.9      false         整数、浮点数、字符
<=        小于等于    'M'<=88     true            整数、浮点数、字符
```

####相等性运算符
== 和 != 这两个关系运算符比较的是两个相似事物的是否相等。

对于整数和浮点数之间的相等性如果值相等就相等。
对于引用类型的变量之间，看他们是否引用了同一个对象，引用地址相等就相等。

####instanceof
instanceof 运算符只值能用于比较对象引用变量，可以检查对象是否是某种类型。这里的类型是指类、接口类型数组

####三目运算符
对于条件表达式b?x:y，先计算条件b，然后进行判断。如果b的值为true，计算x的值，运算结果为x的值；否则，计算y的值，运算结果为y的值。

####运算符优先级

+ () [] .
+ ! +(正) -(负) ~ ++ –
+ * / %
+ +(加) -(减)
+ << >> >>>
+ < <= > >= instanceof
+ == !=
+ &(按位与)
+ ^
+ |
+ &&
+ ||
+ ?:
+ = += -= *= /= %= &= |= ^= ~= <<= >>= >>>=

###9). 流程控制
####if-else分支控制语句
if后的小括号不能省略，括号里表达式的值最终必须返回的是布尔值  
如果条件体内只有一条语句需要执行，那么if后面的大括号可以省略  
对于给定的if，else语句是可选的，else if 语句也是可选的  
else和else if同时出现时，else必须出现在else if之后  
如果有多条else if语句同时出现，那么如果有一条else if语句的表达式测试成功，那么会忽略掉其他所有else if和else分支。  
如果出现多个if,只有一个else的情形，else子句归属于最内层的if语句

if(){}  
if(){}else{}  
if(){}else if(){}  
if(){}else if(){}else{}

####switch分支控制语句
switch表达式必须能被求值成char byte short int 或 enum  
case常量必须是编译时解析的常量

switch(){case}  
switch(){case,default}  
switch(){case,break,default}

####while循环
当条件为真时执行while循环，一直到条件为假时再退出循环体。while括号后的表达式，要求和if语句一样是一个布尔值，判断是否进入循环的条件。

####do-while循环
进行循环之前，先执行一次代码。其他操作同while循环。

####for循环
当知道可以循环多少次时，是使用for循环的最佳时机。

for循环的三个部分任意部分都可以省略，最简单的for循环就是这样的 for(;;){ }
中间的条件表达式必须返回一个布尔值，用来作为是否进行循环的判断依据
初始化语句可以由初始化多个变量，多个变量之间可以用逗号隔开，在for循环中声明的变量作用范围就只在for循环内部
最后的迭代语句一般是i++,j++ 这样的表达式。

####for-each循环
for-each循环又叫增强型for循环，它用来遍历数组和集合中的元素

for(type var : arr) {}

####跳出循环 break 、continue
break:用来终止循环或switch语句
continue:用来终止循环的当前迭代
当存在多层循环时，不带标签的break和continue只能终止离它所在的最内层循环

###10). 面向对象
####类和对象的概念
人类自古就喜欢听故事，也喜欢写故事，我们从小也被要求写作文，为了帮助你写作文。老师还总结了一些规律，譬如记叙文六要素：时间、地点、人物、起因、经过、结果。 有了这样指导性的东西，我们写作文的时候就简单了许多。

面向对象程序语言的核心思想就是把一个事物的状态和行为封装起来作为一个整体看待。类描述的就是对象知道知道什么和执行什么。

举例一架飞机：
以顾客角度看飞机，名字波音777，座位数380人，飞行速度940公里每小时，行为飞行从A地送到B地。
以航空公司角度看飞机，名字波音777，资产编号HNHK20100321，购买价格18.7亿人民币，行为能赚钱。

我们从不同角度去看待和抽象同一架飞机它的状态和行为不相同。

####类和对象的关系
类是对象的骨架，对象是根据骨架建造出来的实例。

举例我们设计一个模拟WOW的格斗游戏
人和怪兽战斗，战斗需要武器。那么圣骑士就是个"类"，人类圣骑士"锦马超"就是一个对象。双手剑件是个类，那么拿在“锦马超”手里的“霜之哀伤”就是一个对象。

举例我们要建立一个全班同学的通讯录
设计一个通讯录的格式，包括姓名、性别、手机号、QQ号、宿舍号。然后我们按照一定的格式印出来，交由每个同学填写，那么每个同学填写的那一份就叫对象，我们填写的通讯录格式本身就是类。

####定义类创建对象
定义一个类的步骤是：定义类名，编写类的属性（状态），编写类的方法（行为）

```{bash}
public class Dog {

    private int size;// 定义了狗的大小的属性

    public void setSize(int size) {// 定义设置大小的方法
        if (size > 0 && size < 10) {
            this.size = size;
        } else {
            size = 1;
        }
    }

    public int getSize() {// 定义获取大小的方法
        return size;
    }

    public void bark(){  // 定义狗叫的方法
        if(size<5){
            System.out.println("汪汪汪!");
        }else{
            System.out.println("嗷!嗷!");
        }      
    }

    public static void main(String[] args) {//定义main方法

        Dog xiaoHang = new Dog();//创建了名字叫小黄的狗对象
        xiaoHang.setSize(3);//设置它的大小属性
        xiaoHang.bark(); //调用它的叫方法

        Dog daHang = new Dog();//创建了名字叫大黄的狗对象
        daHang.setSize(7); //设置它的大小属性
        daHang.bark();//调用它的叫方法
    }
}
```

####面向对象的三大特性(封装、继承、多态)
**封装(encapsulation)**：就是把属性私有化，提供公共方法访问私有对象。 有两层意思，第一隐藏数据，第二把数据和对数据操作的方法绑定。

1. 修改属性的可见性来限制对属性的访问。
2. 为每个属性创建一对赋值方法和取值方法，用于对这些属性的访问。
3. 在赋值和取值方法中，加入对属性的存取限制。

封装的优点：
1. 隐藏类的实现细节；
2. 可加入控制逻辑，限制对属性的不合理操作；
3. 便于修改，增强代码的可维护性；

**继承(inheritance)**：同类事物之间有它的共同性也有各自的独特性，把共同的部分抽离出来作为父类，保留自己特有的属性和方法做为子类。通过继承让子类也具有父类的属性和方法。

举例：马儿都有四条腿，马儿都有会跑
这些共同性抽象出来就成了马类；其中有一些马是白色的马，还有一些是黑色的马，就成了白马类和黑马类。那么白马类和马类之间的关系就是继承关系。它们是父子关系，马类是父类、白马类是子类。
继承的优点：

简化了人们对事物的认识和描述，清晰的体现了相关类间的层次关系。
功能抽象、继承促进了代码复用、继承也带来了多态性。

**多态(polymorphism)**：多态就是“一种定义、多种实现” 。
当父类对象引用变量引用子类对象时，被引用对象的类型决定了调用的成员方法，但是这个被调用的方法必须是在超类中定义过的，也就是说被子类覆盖的方法。

```{bash}
public abstract class Vehicle { 
      abstract void go(Address address);
}

public class Car extends Vehicle{ 
      @Override public void go(Address address){ 
        System.out.println("Car to " + address.getName());
      }
}

public class Plane extends Vehicle{ 
      @Override void go(Address address) { 
        System.out.println("Plane to " + address.getName());
      }
 }

 //多态，父类引用指向子类对象，实际传过来的是抽象类Vehicle的子类，然后编译器会根据具体实现类，来找实现方法。
public void drive(Vehicle v){ 
　　  v.go(new Address("杭州(abstract)"));
}
```

####多态的优点

+ 可替换性。多态对已存在的代码具有可替换性。
+ 可扩充性。增加新的子类不影响已存在类的多态性、继承性，以及其他特性的运行和操作。
+ 接口性。多态是超类通过方法签名，向子类提供了一个共同接口，由子类来完善或者覆盖它而实现的
+ 灵活性。它在应用中体现了灵活多样的操作，提高了使用效率。
+ 简化性。多态简化了对应用软件的代码编写和修改过程，尤其在处理大量对象的运算和操作时，这个特点尤为突出和重要。值得注意的是，多态并不能够解决提高执行速度的问题，因为它基于动态装载和地址引用，或称动态绑定。

###11). 包：类的组织和管理方式
包的用途有以下三种：

+ 将功能相近的类放在同一个包里，方便使用和查找
+ 类名相同的文件可以放在不同的包里而不会产生冲突
+ 可以依据包设定访问权限

###12). 方法：类或对象的行为
####方法的声明
修饰符，可选，用于指定谁有权限访问此方法。
返回值类型，必选，用于指定该方法的返回值数据类型；如果该方法没有返回值，则要用关键字 void 进行标示。方法的返回值只能有一个。
参数列表，可以有0到多个，多个参数之间要用逗号隔开，参数的写法形如：String[] args, int age 这样。
方法名，必选，这个……，好吧命名规则是方法名和变量名的首字母要小写，别丢我人，弄个大写方法名出来。
方法体，可选，这个……，
大括号，大括号不写的方法叫抽象方法。

```{bash}
public void go(int size) throws Exception;
```

####方法重写override
当子类继承父类时，如果子类方法的签名和父类方法的签名相同时，子类的方法就覆盖了父类的方法，我们称之为重写。
举例：动物会喝水，但是猫喝水和人喝水的具体行为就不同。

重写规则：
参数列表必须与重写的方法的参数列表完全匹配（方法签名相同）。如果不匹配，你得到的将是方法重载。
返回类型必须与超类中被重写方法中原先声明的返回类型或其子类型相同。
访问级别的限制性可以比被重写方法弱，但是访问级别的限制性一定不能比被重写方法的更严格。
仅当实例方法被子类继承时，它们才能被重写。子类和超类在同一个包内时，子类可以重写未标示为private和final的任何超类方法。不同包的子类只能重写标示为public或protected的非final方法。
无论父类的方法是否抛出某种运行时异常，子类的重写方法都可以抛出任意类型的运行时异常。
重写方法一定不能抛出比被重写方法声明的检验异常更新或更广的检验异常，可以抛出更少或更有限的异常。
不能重写标示为final的方法。
不能重写标示为static的方法。
如果方法不能被继承，那么方法不能被重写。

####方法重载overload
在Java 中允许类定义中多个方法的方法名相同，只要它们的参数声明不同即可，这种方法就被称为重载（overloaded)。如狗的叫声，参数不同叫声也不同。

重载规则：
+ 重载方法必须改变方法参数列表
+ 重载方法可以改变返回类型
+ 重载方法可以改变访问修饰符
+ 重载方法可以声明新的或更广的检验异常
+ 方法能够在同一个类或者一个子类中被重载

####13) main方法:
main()方法是Java应用程序的入口方法。这个方法和其他的方法有很大的不同，比如方法的名字必须是main，方法必须是public static void 类型的，方法必须接收一个字符串数组的参数等等。

```{bash}
public staitc void main(String[] args){}
```

###14) 构造函数
在Java中，对象是构造出来的，用new关键字来标示这个创建的过程。  
构造函数或者说构造方法不是方法。它们之间的三大区别：

+ 构造函数没有返回值
+ 构造函数名称和类名相同
+ 构造函数不能用final,static,abstract

###15). 接口
####为什么要有接口
Java中只支持单继承，只能有一个父类。为了提供类似多重继承的功能，Java提供了接口的功能。

+ 接口的几个规则
+ 接口名用 interface 修饰, 相对应的 类名用 class修饰
+ 接口里定义的方法都是抽象方法，可以用abstract修饰，当然也可以不用它修饰
+ 接口只能被实现 ( implements )
+ 可以用抽象类实现接口，也就是说虽然实现了，但是没有真正写接口的任何方法，它把责任推给了抽象类的子类
+ 普通类实现接口，则必须按照接口的契约，实现接口所定义的所有方法。
+ 接口可以继承接口，或者说一个接口可以是另一个接口的父类
+ 一个接口可以继承多个父类，也就是说一个接口之间可以多重继承。

###16). 抽象类和抽象方法
+ 用abstract 修饰的类定义，我们称之为抽象类，抽象类不能被实例化。
+ 用abstract 修饰的方法，我们称之为抽象方法，抽象方法不能有方法体。

面向对象中，所有的对象都是某一个类的实例，但是并不是每个类都可以实例化成一个对象。如果一个类中没有足够的信息来描绘一个具体的对象，那么这个类就不能被实例化，我们称之为抽象类。  
抽象类实际上是定义了一个标准和规范，等着他的子类们去实现，譬如动物这个抽象类里定义了一个发出声音的抽象方法，它就定义了一个规则，那就是谁要是动物类的子类，谁就要去实现这个抽象方法。

###17). 初始化块
我们已经知道在类中有两个位置可以放置执行操作的代码，这两个位置是方法和构造函数。初始化块是第三个可以放置执行操作的位置。当首次加载类（静态初始化块）或者创建一个实例（实例初始化块）时，就会运行初始化块。

+ 初始化块的语法相当简单，它没有名称，没有参数，也没有返回值，只有一个大括号，用static 修饰的初始化块就要静态初始化块。
+ 静态初始化块在首次加载类时会运行一次。
+ 实例初始化块在每次创建对象时会运行一次。
+ 实例初始化块在构造函数的super()调用之后运行。
+ 初始化块之间的运行顺序取决于他们在类文件中出现的顺序，出现在前面的先执行。
+ 初始化块从书写惯例上应该写在靠近类文件的顶部，构造函数附近的某个位置。

###18). 内部类
####普通内部类
所谓内部类，就是嵌套在类中的类。被嵌套的类叫外部类。

```{bash}
public class Outer {   
    public class Inner{
    }
}
```

内部类就像一个实例成员一样存在于外部类中。
内部类可以访问外部类的所有成员就想访问自己的成员一样没有限制。
内部类中的this指的是内部类的实例对象本身，如果要用外部类的实例对象就可以用类名.this的方式获得。
内部类对象中不能有静态成员，原因很简单，内部类的实例对象是外部类实例对象的一个成员。

内部类的创建方法：

在外部类的内部，可以用 Inner inner = new Inner(); 方法直接创建
在外部类外部，必须先创建外部类实例，然后再创建内部类实例。
Outer outer = new Outer();
Inner inner = outer.new Inner();










































