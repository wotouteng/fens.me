开发kettle插件 环境搭建
=========

[无所不能的Java系列文章](http://blog.fens.me/series-java/)，涵盖了Java的思想，应用开发，设计模式，程序架构等，通过我的经验去诠释Java的强大。

说起Java，真的有点不知道从何说起。Java是一门全领域发展的语言，从基础的来讲有4大块，Java语法，JDK，JVM，第三方类库。官方又以面向不同应用的角度，又把JDK分为JavaME，JavaSE，JavaEE三个部分。Java可以做客户端界面，可以做中间件，可以做手机系统，可以做应用，可以做工具，可以做游戏，可以做算法…，Java几乎无所不能。

在Java的世界里，Java就是一切。

#### 关于作者

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/java-kettle-plugin-eclipse

![开发kettle插件 环境搭建](http://blog.fens.me/wp-content/uploads/2013/09/kettle-plugin.png)

#### 前言

Kettle一个开源的ETL工具，提供了一套界面操作的解决方案，从而代替原有的程序开发。但有时我们还需要开发自己的插件，来满足我们的业务需求。Kettle基于Eclipse的架构系统，通过JAVA作为客户端的实现。强大的ETL功能，和图形界面的操作，让Kettle成为免费的ETL工具的首选。

#### 目录

1. Kettle插件开发介绍
2. 搭建kettle源代码环境
3. 在Eclipse中构建kettle项目
4. 在Eclipse中构建插件项目
5. 配置插件到Kettle中
6. Kettle项目启动
7. 在kettle项目集成插件源代码

## 1. Kettle插件开发介绍

在我们做ETL工作的时候，在某些项目中往往会遇到一些特别的流程任务，kettle原有的流程处理节点已经不能满足我们的要求，这时候我们就需要定制流程处理节点了。定制流程节点主要是针对数据的管理、数据的验证和某些特别文件数据的提取。大家通过查看kettle源代码，就可以知道怎样去创建你自己的kettle插件了。

Kettle的插件开发，需要依赖于Kettle的源代码环境。

## 2. 搭建kettle源代码环境

### 1). 我的系统环境

* Win7: 64bit desktop
* Java: 64bit 1.6.0_45

kettle源在svn上面，我们需要下载SVN工具，然后才能下载源代码。

### 2). 下载svn工具:Subversion 1.8.3 (Windows 64-bit), 注册后下载

```{bash}
http://www.collab.net/downloads/subversion
```

### 3). 安装Subversion

### 4). 下载kettle源代码

```{bash}
~ D:\workspace\java>svn co http://source.pentaho.org/svnkettleroot/Kettle/tags/4.4.0-stable/ kettle

A    kettle\.directory
A    kettle\.project
A    kettle\cobertura
A    kettle\cobertura\cobertura.jar
A    kettle\cobertura\lib
A    kettle\cobertura\lib\log4j-1.2.9.jar
A    kettle\cobertura\lib\LICENSE
A    kettle\cobertura\lib\javancss.jar
A    kettle\cobertura\lib\junit.jar
A    kettle\cobertura\lib\cpl-v10.html
A    kettle\cobertura\lib\jakarta-oro-2.0.8.jar
A    kettle\cobertura\lib\asm-2.1.jar
A    kettle\cobertura\lib\ccl.jar
A    kettle\src
A    kettle\src\kettle-steps.xml
A    kettle\src\kettle-job-entries.xml
A    kettle\src\kettle-import-rules.xml
A    kettle\src\org
A    kettle\src\org\pentaho
A    kettle\src\org\pentaho\xul
A    kettle\src\org\pentaho\xul\swt
A    kettle\src\org\pentaho\reporting
A    kettle\src\org\pentaho\reporting\plugin
A    kettle\src\org\pentaho\hadoop
A    kettle\src\org\pentaho\hadoop\HadoopCompression.java
A    kettle\src\org\pentaho\di
A    kettle\src\org\pentaho\di\repository
A    kettle\src\org\pentaho\di\repository\kdr
A    kettle\src\org\pentaho\di\repository\kdr\KettleDatabaseRepositorySecurityProvider.java
A    kettle\src\org\pentaho\di\repository\kdr\KettleDatabaseRepositoryCreationHelper.java
A    kettle\src\org\pentaho\di\repository\kdr\KettleDatabaseRepositoryMeta.java
A    kettle\src\org\pentaho\di\repository\kdr\KettleDatabaseRepositoryBase.java
A    kettle\src\org\pentaho\di\repository\kdr\KettleDatabaseRepository.java
A    kettle\src\org\pentaho\di\repository\kdr\delegates
A    kettle\src\org\pentaho\di\repository\kdr\delegates\KettleDatabaseRepositoryBaseDelegate.java
```

下载的非常慢，不可以忍了。

查看SVN服务器位置：

```{bash}
~ ping source.pentaho.org
正在 Ping source.pentaho.org [74.205.95.173] 具有 32 字节的数据:
来自 74.205.95.173 的回复: 字节=32 时间=210ms TTL=50
来自 74.205.95.173 的回复: 字节=32 时间=209ms TTL=50
来自 74.205.95.173 的回复: 字节=32 时间=211ms TTL=50
来自 74.205.95.173 的回复: 字节=32 时间=210ms TTL=50
```

![](http://blog.fens.me/wp-content/uploads/2013/09/kettle-svn.png)

发现SVN服务器在美国！！换另外一种思路，下载源代码！

### 5). 在github上面做了一个clone版

* a. 在一台美国的vps通过svn下载代码。(30s下载完成)
* b. 在github上面新建一个git项目
* c. 增加gitignore屏蔽.svn目录
* d. 上传到自己的github的库里面
* e. 在本地的开发环境从github下载代码

```{bash}
git clone https://github.com/bsspirit/kettle-4.4.0-stable.git
```

### 6). 下载完成，执行ant

```{bash}
~ D:\workspace\java\kettle>ant
Buildfile: D:\workspace\java\kettle\build.xml

init:
     [echo] Init...
    [mkdir] Created dir: D:\workspace\java\kettle\build
    [mkdir] Created dir: D:\workspace\java\kettle\classes
    [mkdir] Created dir: D:\workspace\java\kettle\classes\META-INF
    [mkdir] Created dir: D:\workspace\java\kettle\classes-ui
    [mkdir] Created dir: D:\workspace\java\kettle\classes-ui\ui
    [mkdir] Created dir: D:\workspace\java\kettle\classes-core
    [mkdir] Created dir: D:\workspace\java\kettle\classes-db
    [mkdir] Created dir: D:\workspace\java\kettle\classes-dbdialog
    [mkdir] Created dir: D:\workspace\java\kettle\testClasses
    [mkdir] Created dir: D:\workspace\java\kettle\lib
    [mkdir] Created dir: D:\workspace\java\kettle\distrib
    [mkdir] Created dir: D:\workspace\java\kettle\osx-distrib
    [mkdir] Created dir: D:\workspace\java\kettle\docs\api
    [mkdir] Created dir: D:\workspace\java\kettle\webstart
    [mkdir] Created dir: D:\workspace\java\kettle\junit
    [mkdir] Created dir: D:\workspace\java\kettle\pdi-ce-distrib
     [echo] Revision set to r1

compile-core:
     [echo] Compiling Kettle CORE...
    [javac] Compiling 196 source files to D:\workspace\java\kettle\classes-core

copy-core:
     [echo] Copying core images etc to classes directory...
     [copy] Copying 73 files to D:\workspace\java\kettle\classes-core

kettle-core:
     [echo] Generating the Kettle core library kettle-core.jar ...
      [jar] Building jar: D:\workspace\java\kettle\lib\kettle-core.jar

compile-db:
     [echo] Compiling Kettle DB...
    [javac] Compiling 66 source files to D:\workspace\java\kettle\classes-db

copy-db:
     [echo] Copying db images etc to classes-db directory...
     [copy] Copying 9 files to D:\workspace\java\kettle\classes-db

kettle-db:
     [echo] Generating the Kettle DB library kettle-db.jar ...
      [jar] Building jar: D:\workspace\java\kettle\lib\kettle-db.jar

compile:
     [echo] Compiling Kettle...
    [javac] Compiling 1138 source files to D:\workspace\java\kettle\classes
    [javac] D:\workspace\java\kettle\src\org\pentaho\di\job\entry\JobEntryDialogInterface.java:37: 警告：编码 GBK 的不可
映射字符
    [javac]  *
	If the user changed any settings, the JobEntryInterface object抯 揷hanged?flag must be set to true
[javac] ^
[javac] D:\workspace\java\kettle\src\org\pentaho\di\job\entry\JobEntryDialogInterface.java:43: 警告：编码 GBK 的不可
映射字符
[javac] *The JobEntryInterface object抯 揷hanged?flag must be set to the value it had at the time the dialog o
pened
	
[javac] ^
[javac] D:\workspace\java\kettle\src\org\pentaho\di\job\entry\JobEntryInterface.java:75: 警告：编码 GBK 的不可映射字
符
[javac] * public void loadXML(?
[javac] ^
[javac] D:\workspace\java\kettle\src\org\pentaho\di\job\entry\JobEntryInterface.java:81: 警告：编码 GBK 的不可映射字
符
[javac] * public void saveRep(?
[javac] ^
[javac] D:\workspace\java\kettle\src\org\pentaho\di\job\entry\JobEntryInterface.java:89: 警告：编码 GBK 的不可映射字
符
[javac] * public void loadRep(?
[javac] ^
[javac] D:\workspace\java\kettle\src\org\pentaho\di\trans\steps\mondrianinput\MondrianHelper.java:121: 警告：[deprec
ation] mondrian.olap.Connection 中的 execute(mondrian.olap.Query) 已过时
[javac] result = connection.execute(query);
[javac] ^
[javac] 6 警告copy:
[echo] Copying images etc to classes directory...
[copy] Copying 1884 files to D:\workspace\java\kettle\classes
[copy] Copying 1 file to D:\workspace\java\kettle\classes\META-INF

kettle:
[echo] Generating the Kettle library kettle-engine.jar ...
[jar] Building jar: D:\workspace\java\kettle\lib\kettle-engine.jar

compile-dbdialog:
[echo] Compiling Kettle DB...
[javac] Compiling 5 source files to D:\workspace\java\kettle\classes-dbdialog

copy-dbdialog:
[echo] Copying db images etc to classes-dbdialog directory...
[copy] Copying 23 files to D:\workspace\java\kettle\classes-dbdialog

kettle-dbdialog:
[echo] Generating the Kettle DB library kettle-dbdialog.jar ...
[jar] Building jar: D:\workspace\java\kettle\lib\kettle-dbdialog.jar

compile-ui:
[echo] Compiling Kettle UI...
[javac] Compiling 585 source files to D:\workspace\java\kettle\classes-ui
[javac] D:\workspace\java\kettle\src-ui\org\pentaho\di\ui\job\entries\getpop\JobEntryGetPOPDialog.java:2102: 警告：
编码 GBK 的不可映射字符
[javac] mb.setMessage("Veuillez svp donner un nom 锟?cette entr锟絜 t锟絚he!");
[javac] ^
[javac] 1 警告
[copy] Copying 200 files to D:\workspace\java\kettle\classes-ui
[copy] Copying 379 files to D:\workspace\java\kettle\classes-ui\ui

kettle-ui:
[echo] Generating the Kettle library kettle-ui-swt.jar ...
[jar] Building jar: D:\workspace\java\kettle\lib\kettle-ui-swt.jar

antcontrib.download-check:

antcontrib.download:
[mkdir] Created dir: C:\Users\Administrator\.subfloor\tmp
[get] Getting: http://downloads.sourceforge.net/ant-contrib/ant-contrib-1.0b3-bin.zip
[get] To: C:\Users\Administrator\.subfloor\tmp\antcontrib.zip
[get] http://downloads.sourceforge.net/ant-contrib/ant-contrib-1.0b3-bin.zip permanently moved to http://downloads
.sourceforge.net/project/ant-contrib/ant-contrib/1.0b3/ant-contrib-1.0b3-bin.zip
[get] http://downloads.sourceforge.net/project/ant-contrib/ant-contrib/1.0b3/ant-contrib-1.0b3-bin.zip moved to ht
tp://jaist.dl.sourceforge.net/project/ant-contrib/ant-contrib/1.0b3/ant-contrib-1.0b3-bin.zip
[unzip] Expanding: C:\Users\Administrator\.subfloor\tmp\antcontrib.zip into C:\Users\Administrator\.subfloor\tmp
[copy] Copying 5 files to C:\Users\Administrator\.subfloor\ant-contrib

install-antcontrib:

compile-plugins-standalone:
[echo] Compiling Kettle Plugin kettle-gpload-plugin...
[mkdir] Created dir: D:\workspace\java\kettle\src-plugins\kettle-gpload-plugin\bin\classes
[javac] Compiling 5 source files to D:\workspace\java\kettle\src-plugins\kettle-gpload-plugin\bin\classes
[copy] Copying 7 files to D:\workspace\java\kettle\src-plugins\kettle-gpload-plugin\bin\classes
[echo] Compiling Kettle Plugin kettle-palo-plugin...
[mkdir] Created dir: D:\workspace\java\kettle\src-plugins\kettle-palo-plugin\bin\classes
[javac] Compiling 17 source files to D:\workspace\java\kettle\src-plugins\kettle-palo-plugin\bin\classes
[copy] Copying 28 files to D:\workspace\java\kettle\src-plugins\kettle-palo-plugin\bin\classes
[echo] Compiling Kettle Plugin kettle-hl7-plugin...
[mkdir] Created dir: D:\workspace\java\kettle\src-plugins\kettle-hl7-plugin\bin\classes
[javac] Compiling 13 source files to D:\workspace\java\kettle\src-plugins\kettle-hl7-plugin\bin\classes
[copy] Copying 14 files to D:\workspace\java\kettle\src-plugins\kettle-hl7-plugin\bin\classes
[echo] Compiling Kettle Plugin market...
[mkdir] Created dir: D:\workspace\java\kettle\src-plugins\market\bin\classes
[javac] Compiling 9 source files to D:\workspace\java\kettle\src-plugins\market\bin\classes
[javac] D:\workspace\java\kettle\src-plugins\market\src\org\pentaho\di\core\market\Market.java:533: 警告：[deprecati
on] org.pentaho.di.ui.core.gui.GUIResource 中的 reload() 已过时
[javac] GUIResource.getInstance().reload();
[javac] ^
[javac] 1 警告
[copy] Copying 2 files to D:\workspace\java\kettle\src-plugins\market\bin\classes

compile-plugins:

kettle-plugins-jar-standalone:
[echo] Generating the Kettle Plugin Jar ${plugin} ...
[mkdir] Created dir: D:\workspace\java\kettle\src-plugins\kettle-gpload-plugin\dist
[jar] Building jar: D:\workspace\java\kettle\src-plugins\kettle-gpload-plugin\dist\kettle-gpload-plugin.jar
[echo] Generating the Kettle Plugin Jar ${plugin} ...
[mkdir] Created dir: D:\workspace\java\kettle\src-plugins\kettle-palo-plugin\dist
[jar] Building jar: D:\workspace\java\kettle\src-plugins\kettle-palo-plugin\dist\kettle-palo-plugin.jar
[echo] Generating the Kettle Plugin Jar ${plugin} ...
[mkdir] Created dir: D:\workspace\java\kettle\src-plugins\kettle-hl7-plugin\dist
[jar] Building jar: D:\workspace\java\kettle\src-plugins\kettle-hl7-plugin\dist\kettle-hl7-plugin.jar
[echo] Generating the Kettle Plugin Jar ${plugin} ...
[mkdir] Created dir: D:\workspace\java\kettle\src-plugins\market\dist
[jar] Building jar: D:\workspace\java\kettle\src-plugins\market\dist\market.jar

kettle-plugins-jar:

kettle-plugins-standalone:
[echo] Staging the Kettle plugin kettle-gpload-plugin ...
[mkdir] Created dir: D:\workspace\java\kettle\src-plugins\kettle-gpload-plugin\bin\stage\kettle-gpload-plugin
[copy] Copying 1 file to D:\workspace\java\kettle\src-plugins\kettle-gpload-plugin\bin\stage\kettle-gpload-plugin
[mkdir] Created dir: D:\workspace\java\kettle\src-plugins\kettle-gpload-plugin\bin\stage\kettle-gpload-plugin\lib
[echo] Staging the Kettle plugin kettle-palo-plugin ...
[mkdir] Created dir: D:\workspace\java\kettle\src-plugins\kettle-palo-plugin\bin\stage\kettle-palo-plugin
[copy] Copying 1 file to D:\workspace\java\kettle\src-plugins\kettle-palo-plugin\bin\stage\kettle-palo-plugin
[mkdir] Created dir: D:\workspace\java\kettle\src-plugins\kettle-palo-plugin\bin\stage\kettle-palo-plugin\lib
[copy] Copying 1 file to D:\workspace\java\kettle\src-plugins\kettle-palo-plugin\bin\stage\kettle-palo-plugin\lib
[echo] Staging the Kettle plugin kettle-hl7-plugin ...
[mkdir] Created dir: D:\workspace\java\kettle\src-plugins\kettle-hl7-plugin\bin\stage\kettle-hl7-plugin
[copy] Copying 1 file to D:\workspace\java\kettle\src-plugins\kettle-hl7-plugin\bin\stage\kettle-hl7-plugin
[mkdir] Created dir: D:\workspace\java\kettle\src-plugins\kettle-hl7-plugin\bin\stage\kettle-hl7-plugin\lib
[copy] Copying 10 files to D:\workspace\java\kettle\src-plugins\kettle-hl7-plugin\bin\stage\kettle-hl7-plugin\lib
[echo] Staging the Kettle plugin market ...
[mkdir] Created dir: D:\workspace\java\kettle\src-plugins\market\bin\stage\market
[copy] Copying 1 file to D:\workspace\java\kettle\src-plugins\market\bin\stage\market
[mkdir] Created dir: D:\workspace\java\kettle\src-plugins\market\bin\stage\market\lib
[copy] Copying 1 file to D:\workspace\java\kettle\src-plugins\market\bin\stage\market

kettle-plugins:

compileTests:
[echo] Compiling Kettle tests...
[javac] Compiling 122 source files to D:\workspace\java\kettle\testClasses

kettle-test:
[echo] Generating the Kettle library kettle-test.jar ...
[jar] Building jar: D:\workspace\java\kettle\lib\kettle-test.jar

distrib-nodeps:
[echo] Construct the distribution package...
[copy] Copying 34 files to D:\workspace\java\kettle\distrib
[copy] Copied 10 empty directories to 2 empty directories under D:\workspace\java\kettle\distrib
[mkdir] Created dir: D:\workspace\java\kettle\distrib\lib
[copy] Copying 1 file to D:\workspace\java\kettle\distrib\lib
[copy] Copying 1 file to D:\workspace\java\kettle\distrib\lib
[copy] Copying 1 file to D:\workspace\java\kettle\distrib\lib
[copy] Copying 1 file to D:\workspace\java\kettle\distrib\lib
[copy] Copying 1 file to D:\workspace\java\kettle\distrib\lib
[copy] Copying 1 file to D:\workspace\java\kettle\distrib\lib
[mkdir] Created dir: D:\workspace\java\kettle\distrib\libext
[copy] Copying 214 files to D:\workspace\java\kettle\distrib\libext
[mkdir] Created dir: D:\workspace\java\kettle\distrib\libswt
[copy] Copying 21 files to D:\workspace\java\kettle\distrib\libswt
[mkdir] Created dir: D:\workspace\java\kettle\distrib\plugins
[copy] Copying 15 files to D:\workspace\java\kettle\distrib\plugins
[copy] Copied 11 empty directories to 3 empty directories under D:\workspace\java\kettle\distrib\plugins
[copy] Copying 1 file to D:\workspace\java\kettle\distrib\plugins
[copy] Copied 2 empty directories to 1 empty directory under D:\workspace\java\kettle\distrib\plugins
[copy] Copying 2 files to D:\workspace\java\kettle\distrib\plugins
[copy] Copying 11 files to D:\workspace\java\kettle\distrib\plugins
[copy] Copying 2 files to D:\workspace\java\kettle\distrib\plugins
[copy] Copied 2 empty directories to 1 empty directory under D:\workspace\java\kettle\distrib\plugins
[mkdir] Created dir: D:\workspace\java\kettle\distrib\ui
[copy] Copying 387 files to D:\workspace\java\kettle\distrib\ui
[mkdir] Created dir: D:\workspace\java\kettle\distrib\docs
[copy] Copying 354 files to D:\workspace\java\kettle\distrib\docs
[mkdir] Created dir: D:\workspace\java\kettle\distrib\pwd
[copy] Copying 6 files to D:\workspace\java\kettle\distrib\pwd
[mkdir] Created dir: D:\workspace\java\kettle\distrib\launcher
[copy] Copying 3 files to D:\workspace\java\kettle\distrib\launcher
[mkdir] Created dir: D:\workspace\java\kettle\distrib\simple-jndi
[copy] Copying 1 file to D:\workspace\java\kettle\distrib\simple-jndi
[mkdir] Created dir: D:\workspace\java\kettle\distrib\samples
[mkdir] Created dir: D:\workspace\java\kettle\distrib\samples\transformations
[mkdir] Created dir: D:\workspace\java\kettle\distrib\samples\jobs
[mkdir] Created dir: D:\workspace\java\kettle\distrib\samples\transformations\output
[mkdir] Created dir: D:\workspace\java\kettle\distrib\samples\jobs\output
[copy] Copying 248 files to D:\workspace\java\kettle\distrib\samples

distrib:

default:

BUILD SUCCESSFUL
Total time: 1 minute 29 seconds
```

虽然，有一些警告，但是build成功！！

## 3. 在Eclipse中构建kettle项目

### 7). 把kettle项目，导入到Eclipse中。

![](http://blog.fens.me/wp-content/uploads/2013/09/kettle-eclipse.png)

## 4. 在Eclipse中构建插件项目

### 8). 构建插件项目：我可以基于一个模板去构建插件。

下载kettle-TemplatePlugin项目

```{bash}
wget http://www.ahuoo.com/download/TemplateStepPlugin.rar
```

### 9). 解压后导入到eclipse工程：kettle-TemplatePlugin

复制类库

* 从kettle项目中，复制lib的*.jar到kettle-TemplatePlugin中的libext目录
* 从kettle项目中，复制libswt/win64的swt.js到kettle-TemplatePlugin中的libswt/win64目录

### 10). 刚才复制的类库加入项目依赖

![](http://blog.fens.me/wp-content/uploads/2013/09/kettle-eclipse-template.png)

### 11). 在kettle-TemplatePlugin项目，执行ant

```{bash}
~ D:\workspace\java\kettle-TemplatePlugin>ant
Buildfile: D:\workspace\java\kettle-TemplatePlugin\build.xml

init:
     [echo] Init...

compile:
     [echo] Compiling Jasper Reporting Plugin...
    [javac] D:\workspace\java\kettle-TemplatePlugin\build.xml:40: warning: 'includeantruntime' was not set, defaulting t
o build.sysclasspath=last; set to false for repeatable builds

copy:
     [echo] Copying images etc to classes directory...

lib:
     [echo] Generating the Jasper Reporting library TemplateStepPlugin.jar ...
      [jar] Building jar: D:\workspace\java\kettle-TemplatePlugin\lib\TemplateStepPlugin.jar

distrib:
     [echo] Copying libraries to distrib directory...
     [copy] Copying 1 file to D:\workspace\java\kettle-TemplatePlugin\distrib

deploy:
     [echo] deploying plugin...

default:

BUILD SUCCESSFUL
Total time: 0 seconds
```

### 12). 修改distrib目录的文件

* icon.png：图标文件
* plugin.xml: 插件的配置文件(4.4以后的版本，可以去掉)
* TemplateStepPlugin.jar：是通过ant生成的文件

## 5. 配置插件到Kettle中

### 13). 把kettle-TemplatePlugin发布到kettle中

#### a. 在kettle是工程增加2个目录

```{bash}
~ mkdir D:\workspace\java\kettle\distrib\plugins\steps\myPlugin
~ mkdir D:\workspace\java\kettle\plugins\steps\myPlugin
```

#### b. 修改kettle-TemplatePlugin的build.xml文件

```{bash}
<property name="deploydir" location="D:\workspace\java\kettle\distrib\plugins\steps\myPlugin"/>
<property name="projectdir" location="D:\workspace\java\kettle\plugins\steps\myPlugin"/>

<fileset dir="${libswt}/win64/" includes="*.jar"/>

<target name="deploy" depends="distrib" description="Deploy distribution..." >
<echo>deploying plugin...</echo>
<copy todir="${deploydir}">
<fileset dir="${distrib}" includes="**/*.*"/>
</copy>

<copy todir="${projectdir}">
<fileset dir="${distrib}" includes="**/*.*"/>
</copy>
</target>
```

#### c. kettle-TemplatePlugin项目执行ant

```{bash}
D:\workspace\java\kettle-TemplatePlugin>ant
Buildfile: D:\workspace\java\kettle-TemplatePlugin\build.xml

init:
     [echo] Init...

compile:
     [echo] Compiling Jasper Reporting Plugin...
    [javac] D:\workspace\java\kettle-TemplatePlugin\build.xml:43: warning: 'includeantruntime' was not set, defaulting t
o build.sysclasspath=last; set to false for repeatable builds

copy:
     [echo] Copying images etc to classes directory...

lib:
     [echo] Generating the Jasper Reporting library TemplateStepPlugin.jar ...

distrib:
     [echo] Copying libraries to distrib directory...

deploy:
     [echo] deploying plugin...
     [copy] Copying 3 files to D:\workspace\java\kettle\distrib\plugins\steps\myPlugin
     [copy] Copying 6 files to D:\workspace\java\kettle\distrib\plugins\steps\myPlugin
     [copy] Copying 3 files to D:\workspace\java\kettle\plugins\steps\myPlugin

default:

BUILD SUCCESSFUL
Total time: 0 seconds
```

### 14). 在kettle中查看目录：D:\workspace\java\kettle\distrib\plugins\steps\myPlugin


![](http://blog.fens.me/wp-content/uploads/2013/09/kettle-dist.png)

kettle-TemplatePlugin项目的3个文件，已经被放到了正确的位置

## 6. 命令行项目启动

### 15). 命令行启动kettle

#### a. 修改Spoon启动命令，不开启新窗口，直接以JAVA运行

```{bash}
~ vi D:\workspace\java\kettle\distrib\Spoon.bat

@echo on
REM start "Spoon" "%_PENTAHO_JAVA%" %OPT% -jar launcher\launcher.jar -lib ..\%LIBSPATH% %_cmdline%
java %OPT% -jar launcher\launcher.jar -lib ..\%LIBSPATH% %_cmdline%
```

#### b. 运行Spoon.bat命令

```{bash}
~ D:\workspace\java\kettle\distrib>Spoon.bat

DEBUG: Using JAVA_HOME
DEBUG: _PENTAHO_JAVA_HOME=D:\toolkit\java\jdk6
DEBUG: _PENTAHO_JAVA=D:\toolkit\java\jdk6\bin\javaw

D:\workspace\java\kettle\distrib>REM start "Spoon" "D:\toolkit\java\jdk6\bin\javaw" "-Xmx512m" "-XX:MaxPermSize=256m" "-
Djava.library.path=libswt\win64" "-DKETTLE_HOME=" "-DKETTLE_REPOSITORY=" "-DKETTLE_USER=" "-DKETTLE_PASSWORD=" "-DKETTLE
_PLUGIN_PACKAGES=" "-DKETTLE_LOG_SIZE_LIMIT=" -jar launcher\launcher.jar -lib ..\libswt\win64

D:\workspace\java\kettle\distrib>java "-Xmx512m" "-XX:MaxPermSize=256m" "-Djava.library.path=libswt\win64" "-DKETTLE_HOM
E=" "-DKETTLE_REPOSITORY=" "-DKETTLE_USER=" "-DKETTLE_PASSWORD=" "-DKETTLE_PLUGIN_PACKAGES=" "-DKETTLE_LOG_SIZE_LIMIT="
-jar launcher\launcher.jar -lib ..\libswt\win64
INFO  21-09 12:26:35,717 - Spoon - Logging goes to file:///C:/Users/ADMINI~1/AppData/Local/Temp/spoon_0042f442-2276-11e3
-bf49-6be1282e1ee0.log
INFO  21-09 12:26:36,655 - Spoon - 要求资源库
INFO  21-09 12:26:36,795 - RepositoriesMeta - Reading repositories XML file: C:\Users\Administrator\.kettle\repositories
.xml
INFO  21-09 12:26:37,783 - Version checker - OK
```

### 16). 查看kettle-TemplatePlugin 插件

![](http://blog.fens.me/wp-content/uploads/2013/09/kettle-debug1.png)

## 7. 在kettle项目集成插件源代码

### 17). 通过Eclipse的 link source功能，连接kettle-TemplatePlugin项目

#### a. 在kettle项目中，选择link source

![](http://blog.fens.me/wp-content/uploads/2013/09/kettle-link-source.png)

#### b. 在kettle项目中编程

![](http://blog.fens.me/wp-content/uploads/2013/09/kettle-source.png)

### 18). 通过Eclipse启动kettle

#### a. 在Eclipse中配置启动Main Class: org.pentaho.di.ui.spoon.Spoon

![](http://blog.fens.me/wp-content/uploads/2013/09/kettle-run1.png)

#### b.增加64位的swt.jar类库

![](http://blog.fens.me/wp-content/uploads/2013/09/kettle-run2.png)

#### c. 在Eclipse中启动kettle

![](http://blog.fens.me/wp-content/uploads/2013/09/kettle-run3.png)

### 19). 通过Eclipse调用kettle-TemplatePlugin

#### a. 修改TemplateStepDialog.java，找到open方法，增加一行输出

```{bash}
public String open() { 
System.out.println(“Open a dialog!!!”);

...
}
```

#### b. 在Eclipse中，通过debug启动：org.pentaho.di.ui.spoon.Spoon

双点Template Plugin的图标，看到日志显示”Open a dialog!!!“

![](http://blog.fens.me/wp-content/uploads/2013/09/kettle-debug11.png)

这样我们就构建好了，kettle插件的开发环境。接下来，我们就可以进行插件开发了！！

#### 转载请注明出处：http://blog.fens.me/java-kettle-plugin-eclipse

