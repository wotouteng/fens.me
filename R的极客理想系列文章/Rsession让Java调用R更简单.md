Rsession让Java调用R更简单
=========

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-rserve-rsession/

![Rsession让Java调用R更简单](http://blog.fens.me/wp-content/uploads/2013/11/rsession.png)

#### 前言

写了好几篇关于Rserve的文章了，Rserve作为R语言的通信接口，已经成为了R语言扩展的重要通道。大数据时代的到来，让原本小众的R语言，不经意间挤进了开发语言前20名。

同时，具有多种编程语言背景的IT工程师们，开始进入R的社区，帮助R语言快速进化。Rserve提供了一个通信的接口，通过封装可以让R引擎嵌入到其他语言里。

#### 目录

1. Rsession介绍
2. Rsession下载
3. 用Eclipse构建Rsession项目
4. Rsession的API介绍
5. Rserve服务器系统环境
6. Rsession使用

## 1. Rsession介绍

Rsession提供了一种简单的方式，让Java可以访问远程或本地的Rserve实例。Rsession是对Rserve的封装，提供了更高层的API接口，包括Rserve服务器控制，多会话机制，并支持Windows环境。

另一个R和Java通信的库JRI，并不支持多会话机制。关于R和Java通信的其他文章，请参考：[解惑rJava R与Java的高速通道](http://blog.fens.me/r-rjava-java/), [Rserve与Java的跨平台通信](http://blog.fens.me/r-rserve-java/)

Rsession项目主页：https://code.google.com/p/rsession/

## 2. Rsession下载

### 系统环境

* Win7 64bit
* R: 3.0.1 x86_64-w64-mingw32/x64 b4bit

发行包下载：解压缩就可以直接使用了

http://rsession.googlecode.com/files/libRsession.zip

包括3个jar包：REngine.jar, Rserve.jar, Rsession.jar

源代码下载：(SVN)

http://rsession.googlecode.com/svn/trunk/Rsession/

```{bash}

~ cd d:\workspace\java
~ svn checkout http://rsession.googlecode.com/svn/trunk/ rsession-read-only
~ mv rsession-read-only rsession
~ cd rsession\Rsession
```

项目是通过Ant构建的，我们可以自己编译，打包。

```{bash}
~ ant
Buildfile: d:\workspace\java\rsession\Rsession\build.xml

clean:

clean-dist:

init:
    [mkdir] Created dir: d:\workspace\java\rsession\Rsession\build
    [mkdir] Created dir: d:\workspace\java\rsession\Rsession\dist\lib

resource:
     [copy] Copying 28 files to d:\workspace\java\rsession\Rsession\dist\lib
     [copy] Copied 12 empty directories to 1 empty directory under d:\workspace\java\rsession\Rsession\dist\lib

compile:
    [javac] d:\workspace\java\rsession\Rsession\build.xml:33: warning: 'includeantruntime' was not set, defaulting to bu
ild.sysclasspath=last; set to false for repeatable builds
    [javac] Compiling 10 source files to d:\workspace\java\rsession\Rsession\build

dist:
      [jar] Building jar: d:\workspace\java\rsession\Rsession\dist\lib\Rsession.jar
      [zip] Building zip: d:\workspace\java\rsession\Rsession\dist\libRsession.zip

BUILD SUCCESSFUL
Total time: 2 seconds
```

在目录：d:\workspace\java\rsession\Rsession\dist\下面，生成发行包，libRsession.zip

## 3. 用Eclipse构建Rsession项目

用Eclipse构建Rsession项目，复制Rsession\dist\目录文件到项目，并加载到项目的环境变量。

![](http://blog.fens.me/wp-content/uploads/2013/11/rsession-eclipse.png)

## 4. Rsession的API介绍

查看类库：Rsession.jar

![](http://blog.fens.me/wp-content/uploads/2013/11/rsession-api.png)

接口类

* BusyListener: Notify the state of R engine
* EvalListener: Notify the evaluation of R expression
* Logger: Support R messages printing
* UpdateObjectsListener: Notify the changing of R environment objects

功能类

* Rdaemon: RServe的守护进程
* RLogPanel: 显示R日志的空间
* RObjectsPanel: 显示R变量的控件
* RserverConf: 连接Rserve实例的配置文件
* Rsession: 连接Rserve实例
* StartRserve: 启动本地的Rserve

## 5. Rserve服务器系统环境

### 服务器系统环境

* Linux: Ubuntu 12.04.2 LTS 64bit
* R: 3.0.1 x86_64-pc-linux-gnu
* Rserve: Rserve v1.7-1

### Rserve环境

* IP: 192.168.1.201，允许远程访问
* 端口: 6311
* 登陆认证: 用户名:conan, 密码:conan
* 字符编码: utf-8

Rserve服务器环境与文章中 Rserve的R语言客户端RSclient 配置相同。

## 6. Rsession使用

建立远程连接

```{bash}
RserverConf rconf = new RserverConf("192.168.1.201", 6311, "conan", "conan", new Properties());
Rsession s = Rsession.newInstanceTry(System.out, rconf);
```

执行R脚本

```{bash}
double[] rand = s.eval("rnorm(5)").asDoubles();
for(double ran:rand){
    System.out.print(ran+",");
}

//日志输出
[eval] rnorm(5)
  org.rosuda.REngine.REXPDouble@5f934ad[5]{0.08779203903807914,0.039929482749452114,-0.8788534039223883,-0.8875740206608903,-0.8493446334021442}
0.08779203903807914,0.039929482749452114,-0.8788534039223883,-0.8875740206608903,-0.8493446334021442
```

R创建对象并保存环境

```{bash}
// 创建一个R对象
s.set("demo", Math.random());
s.eval("ls()");

// 保存R环境到本地到文件
s.save(new File("./output/save.Rdata"), "demo");

// 删除R对象demo
s.rm("demo");
s.eval("ls()");

// 从文件加载R环境
s.load(new File("./output/save.Rdata"));
s.eval("ls()");
s.eval("print(demo)");

//日志输出
[set] demo 
```

创建一个data.frame对象

```{bash}
s.set("df", new double[][] { { 1, 2, 3 }, { 4, 5, 6 }, { 7, 8, 9 }, { 10, 11, 12 } }, "x1", "x2", "x3");
double df$x1_3 = s.eval("df$x1[3]").asDouble();
System.out.println(df$x1_3);
s.rm("df");

//日志输出
[set] df 
```

在本地生成一个图形文件

```{bash}
s.toJPEG(new File("./output/plot.png"), 400, 400, "plot(rnorm(10))");

//日志输出
[set] plotfile_1100539400 
```

![](http://blog.fens.me/wp-content/uploads/2013/11/plot.jpg)

以HTML格式输出

```{bash}
String html = s.asHTML("summary(rnorm(100))");
System.out.println(html);

//日志输出
<html>     Min.   1st Qu.    Median      Mean   3rd Qu.      Max. <br/>-2.332000 -0.659900  0.036920  0.004485  0.665800  2.517000 </html>
```

以文本格式输出

```{bash}
String txt = s.asString("summary(rnorm(100))"); // format in text
System.out.println(txt);

//日志输出
    Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
-3.19700 -0.65330 -0.09893 -0.07190  0.53300  2.29000 
```

安装新类库

```{bash}
System.out.println(s.installPackage("sensitivity", true));

//日志输出
  trying to load package sensitivity
  package sensitivity is not installed.
  package sensitivity not yet installed.
[eval] install.packages('sensitivity',repos='http://cran.cict.fr/',dependencies=TRUE)
  org.rosuda.REngine.REXPNull@4d47c5fc
  request package sensitivity install...
  package sensitivity is not installed.
!   package sensitivity installation failed.
Impossible to install package sensitivity !
```

查看完整的文件：RsessionDemo.java

```{bash}
~ vi RsessionDemo.java

package org.conan.r.rsession;

import java.io.File;
import java.util.Properties;

import org.math.R.RserverConf;
import org.math.R.Rsession;
import org.rosuda.REngine.REXPMismatchException;

public class RsessionDemo {

    public static void main(String args[]) throws REXPMismatchException {
        RserverConf rconf = new RserverConf("192.168.1.201", 6311, "conan", "conan", new Properties());
        Rsession s = Rsession.newInstanceTry(System.out, rconf);

        // 执行R脚本
        double[] rand = s.eval("rnorm(5)").asDoubles();
        System.out.println(rand);

        // 创建一个R对象
        s.set("demo", Math.random());
        s.eval("ls()");

        // 保存R运行时状态到文件
        s.save(new File("./output/save.Rdata"), "demo");

        // 删除R对象demo
        s.rm("demo");
        s.eval("ls()");

        // 从文件加载R环境
        s.load(new File("./output/save.Rdata"));
        s.eval("ls()");
        s.eval("print(demo)");

        // 创建一个data.frame对象
        s.set("df", new double[][] { { 1, 2, 3 }, { 4, 5, 6 }, { 7, 8, 9 }, { 10, 11, 12 } }, "x1", "x2", "x3");
        double df$x1_3 = s.eval("df$x1[3]").asDouble();
        System.out.println(df$x1_3);
        s.rm("df");

        // 生成一个图形文件
        s.eval("getwd()");
        s.toJPEG(new File("./output/plot.png"), 400, 400, "plot(rnorm(10))");

        // 以HTML格式输出
        String html = s.asHTML("summary(rnorm(100))"); 
        System.out.println(html);

        // 以文本格式输出
        String txt = s.asString("summary(rnorm(100))");
        System.out.println(txt);

        // 安装新类库
        System.out.println(s.installPackage("sensitivity", true));

        s.end();
    }
}
```

对比Rserve的JavaAPI(参考文章：Rserve与Java的跨平台通信)，是不是感觉Rsession再友好呢！

我们在使用RStudio时，任务管理器中也可以看到rsession的进程！猜一下，RStudio也在使用rsession做程序接口。

![](http://blog.fens.me/wp-content/uploads/2013/11/rsession-process.png)

动起手来，创造就在自己手中。

#### 转载请注明出处：http://blog.fens.me/r-rserve-rsession/

