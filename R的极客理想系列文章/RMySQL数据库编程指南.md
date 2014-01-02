RMySQL数据库编程指南
===========

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-mysql-rmysql/

![RMySQL数据库编程指南](http://blog.fens.me/wp-content/uploads/2013/09/r-rmysql.png)

#### 前言

MySQL是一款最常用到开源数据库软件，安装简单，运行稳定，非常适用于中小型的数据存储。R作为数据分析的工具，当然要支持数据库驱动接口。让R和MySQL配合在一起，所能爆发出的能量是巨大的。

由于操作系统的原因，让Win和Linux有不一样的字符集，不一样的运行时环境。所以，今天我们讲一下如何在Linux和Win上面安装和使用RMySQL。

#### 目录

1. RMySQL介绍
2. RMySQL在Linux下安装
3. RMySQL在Win7下安装
4. RMySQL函数使用
5. RMySQL案例实践

## 1. RMySQL介绍

RMySQL一个R语言程序包，提供了访问MySQL数据库的R语言接口程序，RMySQL需求依赖于DBI项目。RMySQL不仅提供了基本的数据库访问，SQL查询，还封装了一些方法。比较读整表，分页，data.frame快速插入等等的功能。掌握好RMySQL，数据库编辑将得心应手！！

## 2. RMySQL在Linux下安装

### Linux系统环境：

* Linux: Ubuntu 12.04.2 LTS 64bit server
* Linux字符集: en_US.UTF-8
* R: 3.0.1, x86_64-pc-linux-gnu (64-bit)
* MySQL: Ver 14.14 Distrib 5.5.29 64bit server
* MySQL字符集: utf8

```{bash}

~ uname -a
Linux conan 3.5.0-23-generic #35~precise1-Ubuntu SMP Fri Jan 25 17:13:26 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux

~ cat /etc/issue
Ubuntu 12.04.2 LTS \n \l

~ locale
LANG=en_US.UTF-8
LANGUAGE=
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=en_US.UTF-8

~ R --version
R version 3.0.1 (2013-05-16) -- "Good Sport"
Copyright (C) 2013 The R Foundation for Statistical Computing
Platform: x86_64-pc-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under the terms of the
GNU General Public License versions 2 or 3.
For more information about these matters see

http://www.gnu.org/licenses/.

~ mysql --version
mysql  Ver 14.14 Distrib 5.5.29, for debian-linux-gnu (x86_64) using readline 6.2

mysql> show variables like '%char%';
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | utf8                       |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | utf8                       |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
8 rows in set (0.00 sec)
```

### 在R环境中安装RMySQL

```{bash}

~ R
> install.packages('RMySQL')

also installing the dependency ‘DBI’

trying URL 'http://cran.dataguru.cn/src/contrib/DBI_0.2-7.tar.gz'
Content type 'application/x-gzip' length 194699 bytes (190 Kb)
opened URL
==================================================
downloaded 190 Kb

trying URL 'http://cran.dataguru.cn/src/contrib/RMySQL_0.9-3.tar.gz'
Content type 'application/x-gzip' length 165363 bytes (161 Kb)
opened URL
==================================================
downloaded 161 Kb

...

Configuration error:
  could not find the MySQL installation include and/or library
  directories.  Manually specify the location of the MySQL
  libraries and the header files and re-run R CMD INSTALL.

INSTRUCTIONS:

1. Define and export the 2 shell variables PKG_CPPFLAGS and
   PKG_LIBS to include the directory for header files (*.h)
   and libraries, for example (using Bourne shell syntax):

      export PKG_CPPFLAGS="-I"
      export PKG_LIBS="-L -lmysqlclient"

   Re-run the R INSTALL command:

      R CMD INSTALL RMySQL_.tar.gz

2. Alternatively, you may pass the configure arguments
      --with-mysql-dir= (distribution directory)
   or
      --with-mysql-inc= (where MySQL header files reside)
      --with-mysql-lib= (where MySQL libraries reside)
   in the call to R INSTALL --configure-args='...'

   R CMD INSTALL --configure-args='--with-mysql-dir=DIR' RMySQL_.tar.gz

ERROR: configuration failed for package ‘RMySQL’
* removing ‘/home/conan/R/x86_64-pc-linux-gnu-library/3.0/RMySQL’

The downloaded source packages are in
        ‘/tmp/Rtmpu0Gn88/downloaded_packages’
Warning message:
In install.packages("RMySQL") :
  installation of package ‘RMySQL’ had non-zero exit status
```

### 安装出错了，提示我们需要增加MySQL安装目录的配置参数

```{bash}
# 安装mysql类库 
~ sudo apt-get install libdbd-mysql libmysqlclient-dev

# 找到mysql的安装目录
~ whereis mysql
mysql: /usr/bin/mysql /etc/mysql /usr/lib/mysql /usr/bin/X11/mysql /usr/share/mysql /usr/share/man/man1/mysql.1.gz

# 找到刚刚下载的RMySQL_.tar.gz
~ ls /tmp/Rtmpu0Gn88/downloaded_packages
DBI_0.2-7.tar.gz  RMySQL_0.9-3.tar.gz

# 通过命令安装RMySQL
~ R CMD INSTALL --configure-args='--with-mysql-dir=/usr/lib/mysql' /tmp/Rtmpu0Gn88/downloaded_packages/RMySQL_0.9-3.tar.gz

* installing to library ‘/home/conan/R/x86_64-pc-linux-gnu-library/3.0’
* installing *source* package ‘RMySQL’ ...
** package ‘RMySQL’ successfully unpacked and MD5 sums checked
checking for gcc... gcc
checking for C compiler default output file name... a.out
checking whether the C compiler works... yes
checking whether we are cross compiling... no
checking for suffix of executables...
checking for suffix of object files... o
checking whether we are using the GNU C compiler... yes
checking whether gcc accepts -g... yes
checking for gcc option to accept ANSI C... none needed
checking how to run the C preprocessor... gcc -E
checking for compress in -lz... yes
checking for getopt_long in -lc... yes
checking for mysql_init in -lmysqlclient... yes
checking for egrep... grep -E
checking for ANSI C header files... yes
checking for sys/types.h... yes
checking for sys/stat.h... yes
checking for stdlib.h... yes
checking for string.h... yes
checking for memory.h... yes
checking for strings.h... yes
checking for inttypes.h... yes
checking for stdint.h... yes
checking for unistd.h... yes
checking mysql.h usability... no
checking mysql.h presence... no
checking for mysql.h... no
checking /usr/local/include/mysql/mysql.h usability... no
checking /usr/local/include/mysql/mysql.h presence... no
checking for /usr/local/include/mysql/mysql.h... no
checking /usr/include/mysql/mysql.h usability... yes
checking /usr/include/mysql/mysql.h presence... yes
checking for /usr/include/mysql/mysql.h... yes
configure: creating ./config.status
config.status: creating src/Makevars
** libs
gcc -std=gnu99 -I/usr/share/R/include -DNDEBUG -I/usr/include/mysql     -fpic  -O3 -pipe  -g  -c RS-DBI.c -o RS-DBI.o
gcc -std=gnu99 -I/usr/share/R/include -DNDEBUG -I/usr/include/mysql     -fpic  -O3 -pipe  -g  -c RS-MySQL.c -o RS-MySQL.o
gcc -std=gnu99 -shared -o RMySQL.so RS-DBI.o RS-MySQL.o -lmysqlclient -lz -L/usr/lib/R/lib -lR
installing to /home/conan/R/x86_64-pc-linux-gnu-library/3.0/RMySQL/libs
** R
** inst
** preparing package for lazy loading
Creating a generic function for ‘format’ from package ‘base’ in package ‘RMySQL’
Creating a generic function for ‘print’ from package ‘base’ in package ‘RMySQL’
** help
*** installing help indices
** building package indices
** installing vignettes
** testing if installed package can be loaded
* DONE (RMySQL)
```

RMySQL安装成功.

### 在MySQL中建库建表

```{bash}
~ mysql -uroot -p

mysql> create database rmysql;
Query OK, 1 row affected (0.00 sec)

mysql> grant all on rmysql.* to rmysql@'%' identified by 'rmysql';
Query OK, 0 rows affected (0.00 sec)

mysql> grant all on rmysql.* to rmysql@localhost identified by 'rmysql';
Query OK, 0 rows affected (0.00 sec)

mysql> use rmysql
Database changed

mysql> CREATE TABLE t_user(
    -> id INT PRIMARY KEY AUTO_INCREMENT,
    -> user varchar(12) NOT NULL UNIQUE
    -> )ENGINE=INNODB DEFAULT CHARSET=utf8;
Query OK, 0 rows affected (0.07 sec)

mysql> INSERT INTO t_user(user) values('A1'),('AB'),('fens.me');
Query OK, 3 rows affected (0.04 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM t_user;
+----+---------+
| id | user    |
+----+---------+
|  1 | A1      |
|  2 | AB      |
|  3 | fens.me |
+----+---------+
3 rows in set (0.00 sec)
```

### 通过R程序，读MySQL数据库数据

```{bash}
~ R

> library(RMySQL)
Loading required package: DBI

> conn  users = dbGetQuery(conn, "SELECT * FROM t_user")
> dbDisconnect(conn)
[1] TRUE
> users
  id    user
1  1      A1
2  2      AB
3  3 fens.me
```

好了，我们实现了在Linux下R和MySQL的连接。


## 3. RMySQL在Win7下安装

### Win系统环境：

* Win7: 64位 旗舰版
* Win字符集: gbk,utf8
* R: 3.0.1, x86_64-w64-mingw32/x64 (64-bit)
* MySQL: mysql Ver 14.14 Distrib 5.6.11, for Win64 (x86_64)

```{bash}

~ R --version
R version 3.0.1 (2013-05-16) -- "Good Sport"
Copyright (C) 2013 The R Foundation for Statistical Computing
Platform: x86_64-w64-mingw32/x64 (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under the terms of the
GNU General Public License versions 2 or 3.
For more information about these matters see

http://www.gnu.org/licenses/.

~ mysql --version
mysql  Ver 14.14 Distrib 5.6.11, for Win64 (x86_64)

mysql> show variables like '%char%';
+--------------------------+------------------------------------+
| Variable_name            | Value                              |
+--------------------------+------------------------------------+
| character_set_client     | gbk                                |
| character_set_connection | gbk                                |
| character_set_database   | utf8                               |
| character_set_filesystem | binary                             |
| character_set_results    | gbk                                |
| character_set_server     | utf8                               |
| character_set_system     | utf8                               |
| character_sets_dir       | D:\toolkit\mysql56\share\charsets\ |
+--------------------------+------------------------------------+
8 rows in set (0.07 sec)
```

### 在R环境中安装RMySQL

```{bash}
~ D:\workspace\R\mysql>R

> install.packages('RMySQl')
package 'RMySQl' is not available (for R version 3.0.1)
```

### 我们看到提示，没有对应的RMySQL安装版本。

```{bash}
# 下载RMySQL源代码包
> install.packages("RMySQL", type="source")
URLhttp://cran.dataguru.cn/src/contrib/RMySQL_0.9-3.tar.gz'
Content type 'application/x-gzip' length 165363 bytes (161 Kb)
URL
downloaded 161 Kb

* installing *source* package 'RMySQL' ...
** 'RMySQL'MD5
checking for $MYSQL_HOME... not found... searching registry...

cygwin warning:
  MS-DOS style path detected: C:/PROGRA~1/R/R-30~1.1/bin/x64/Rscript
  Preferred POSIX equivalent is: /cygdrive/c/PROGRA~1/R/R-30~1.1/bin/x64/Rscript
  CYGWIN environment variable option "nodosfilewarning" turns off this warning.
  Consult the user's guide for more details about POSIX paths:

http://cygwin.com/cygwin-ug-net/using.html#using-pathnames

readRegistry("SOFTWARE\\MySQL AB", hive = "HLM", maxdepth = 2) :
  Registry key 'SOFTWARE\MySQL AB' not found

ERROR: configuration failed for package 'RMySQL'
* removing 'C:/Program Files/R/R-3.0.1/library/RMySQL'
        'C:\Users\Administrator\AppData\Local\Temp\RtmpsfqQjK\downloaded_packages'

In install.packages("RMySQL", type = "source") :
  'RMySQL'B0
```

### 找到源代码包：RMySQL_0.9-3.tar.gz

```{bash}
~ dir C:\Users\Administrator\AppData\Local\Temp\RtmpsfqQjK\downloaded_packages
2013-09-24  13:16           165,363 RMySQL_0.9-3.tar.gz
```

### 通过源代码包安装

```{bash}
~ D:\workspace\R\mysql>R CMD INSTALL C:\Users\Administrator\AppData\Local\Temp\RtmpsfqQjK\downloaded_packages\RMySQL_0.9-3
.tar.gz
* installing to library 'C:/Program Files/R/R-3.0.1/library'
* installing *source* package 'RMySQL' ...
** 'RMySQL'MD5
checking for $MYSQL_HOME... not found... searching registry...

cygwin warning:
  MS-DOS style path detected: C:/PROGRA~1/R/R-30~1.1/bin/x64/Rscript
  Preferred POSIX equivalent is: /cygdrive/c/PROGRA~1/R/R-30~1.1/bin/x64/Rscript
  CYGWIN environment variable option "nodosfilewarning" turns off this warning.
  Consult the user's guide for more details about POSIX paths:

http://cygwin.com/cygwin-ug-net/using.html#using-pathnames

readRegistry("SOFTWARE\\MySQL AB", hive = "HLM", maxdepth = 2) :
  Registry key 'SOFTWARE\MySQL AB' not found

ERROR: configuration failed for package 'RMySQL'
* removing 'C:/Program Files/R/R-3.0.1/library/RMySQL'
```

### 设置MYSQL_HOME的环境变量

```{bash}
set MYSQL_HOME=D:\toolkit\mysql56
```

注： MYSQL_HOME建议设置在系统环境变量中。

### 再一次安装RMySQL

```{bash}
D:\workspace\R\mysql>R CMD INSTALL C:\Users\Administrator\AppData\Local\Temp\RtmpsfqQjK\downloaded_packages\RMySQL_0.9-3
.tar.gz
* installing to library 'C:/Program Files/R/R-3.0.1/library'
* installing *source* package 'RMySQL' ...
** 'RMySQL'MD5
checking for $MYSQL_HOME... D:\toolkit\mysql56
cygwin warning:
  MS-DOS style path detected: D:\toolkit\mysql56
  Preferred POSIX equivalent is: /cygdrive/d/toolkit/mysql56
  CYGWIN environment variable option "nodosfilewarning" turns off this warning.
  Consult the user's guide for more details about POSIX paths:

http://cygwin.com/cygwin-ug-net/using.html#using-pathnames

** libs
: this package has a non-empty 'configure.win' file,
so building only the main architecture

cygwin warning:
  MS-DOS style path detected: C:/PROGRA~1/R/R-30~1.1/etc/x64/Makeconf
  Preferred POSIX equivalent is: /cygdrive/c/PROGRA~1/R/R-30~1.1/etc/x64/Makeconf
  CYGWIN environment variable option "nodosfilewarning" turns off this warning.
  Consult the user's guide for more details about POSIX paths:

http://cygwin.com/cygwin-ug-net/using.html#using-pathnames

gcc -m64 -I"C:/PROGRA~1/R/R-30~1.1/include" -DNDEBUG -I"D:\toolkit\mysql56"/include    -I"d:/RCompile/CRANpkg/extralibs6
4/local/include"     -O2 -Wall  -std=gnu99 -mtune=core2 -c RS-DBI.c -o RS-DBI.o
RS-DBI.c: In function 'RS_na_set':
RS-DBI.c:1219:11: warning: variable 'c' set but not used [-Wunused-but-set-variable]
gcc -m64 -I"C:/PROGRA~1/R/R-30~1.1/include" -DNDEBUG -I"D:\toolkit\mysql56"/include    -I"d:/RCompile/CRANpkg/extralibs6
4/local/include"     -O2 -Wall  -std=gnu99 -mtune=core2 -c RS-MySQL.c -o RS-MySQL.o
RS-MySQL.c: In function 'RS_MySQL_fetch':
RS-MySQL.c:657:13: warning: variable 'fld_nullOk' set but not used [-Wunused-but-set-variable]
RS-MySQL.c: In function 'RS_DBI_invokeBeginGroup':
RS-MySQL.c:1137:30: warning: variable 'val' set but not used [-Wunused-but-set-variable]
RS-MySQL.c: In function 'RS_DBI_invokeNewRecord':
RS-MySQL.c:1158:20: warning: variable 'val' set but not used [-Wunused-but-set-variable]
RS-MySQL.c: In function 'RS_MySQL_dbApply':
RS-MySQL.c:1219:38: warning: variable 'fld_nullOk' set but not used [-Wunused-but-set-variable]
gcc -m64 -shared -s -static-libgcc -o RMySQL.dll tmp.def RS-DBI.o RS-MySQL.o D:\toolkit\mysql56/bin/libmySQL.dll -Ld:/RC
ompile/CRANpkg/extralibs64/local/lib/x64 -Ld:/RCompile/CRANpkg/extralibs64/local/lib -LC:/PROGRA~1/R/R-30~1.1/bin/x64 -l
R
gcc.exe: error: D:\toolkit\mysql56/bin/libmySQL.dll: No such file or directory
ERROR: compilation failed for package 'RMySQL'
* removing 'C:/Program Files/R/R-3.0.1/library/RMySQL'
```

### 错误为没有找到动态链接库：D:\toolkit\mysql56/bin/libmySQL.dll

```{bash}
# 复制动态链接库libmySQL.dll
cp D:\toolkit\mysql56\lib\libmysql.dll D:\toolkit\mysql56\bin\
mv D:\toolkit\mysql56\bin\libmysql.dll D:\toolkit\mysql56\bin\libmySQL.dll
```

### 再一次安装RMySQL

```{bash}
~ D:\workspace\R\mysql>R CMD INSTALL C:\Users\Administrator\AppData\Local\Temp\RtmpsfqQjK\downloaded_packages\RMySQL_0.9-3
.tar.gz
* installing to library 'C:/Program Files/R/R-3.0.1/library'
* installing *source* package 'RMySQL' ...
** 'RMySQL'MD5
checking for $MYSQL_HOME... D:\toolkit\mysql56
cygwin warning:
  MS-DOS style path detected: D:\toolkit\mysql56
  Preferred POSIX equivalent is: /cygdrive/d/toolkit/mysql56
  CYGWIN environment variable option "nodosfilewarning" turns off this warning.
  Consult the user's guide for more details about POSIX paths:

http://cygwin.com/cygwin-ug-net/using.html#using-pathnames

** libs
: this package has a non-empty 'configure.win' file,
so building only the main architecture

cygwin warning:
  MS-DOS style path detected: C:/PROGRA~1/R/R-30~1.1/etc/x64/Makeconf
  Preferred POSIX equivalent is: /cygdrive/c/PROGRA~1/R/R-30~1.1/etc/x64/Makeconf
  CYGWIN environment variable option "nodosfilewarning" turns off this warning.
  Consult the user's guide for more details about POSIX paths:

http://cygwin.com/cygwin-ug-net/using.html#using-pathnames

gcc -m64 -I"C:/PROGRA~1/R/R-30~1.1/include" -DNDEBUG -I"D:\toolkit\mysql56"/include    -I"d:/RCompile/CRANpkg/extralibs6
4/local/include"     -O2 -Wall  -std=gnu99 -mtune=core2 -c RS-DBI.c -o RS-DBI.o
RS-DBI.c: In function 'RS_na_set':
RS-DBI.c:1219:11: warning: variable 'c' set but not used [-Wunused-but-set-variable]
gcc -m64 -I"C:/PROGRA~1/R/R-30~1.1/include" -DNDEBUG -I"D:\toolkit\mysql56"/include    -I"d:/RCompile/CRANpkg/extralibs6
4/local/include"     -O2 -Wall  -std=gnu99 -mtune=core2 -c RS-MySQL.c -o RS-MySQL.o
RS-MySQL.c: In function 'RS_MySQL_fetch':
RS-MySQL.c:657:13: warning: variable 'fld_nullOk' set but not used [-Wunused-but-set-variable]
RS-MySQL.c: In function 'RS_DBI_invokeBeginGroup':
RS-MySQL.c:1137:30: warning: variable 'val' set but not used [-Wunused-but-set-variable]
RS-MySQL.c: In function 'RS_DBI_invokeNewRecord':
RS-MySQL.c:1158:20: warning: variable 'val' set but not used [-Wunused-but-set-variable]
RS-MySQL.c: In function 'RS_MySQL_dbApply':
RS-MySQL.c:1219:38: warning: variable 'fld_nullOk' set but not used [-Wunused-but-set-variable]
gcc -m64 -shared -s -static-libgcc -o RMySQL.dll tmp.def RS-DBI.o RS-MySQL.o D:\toolkit\mysql56/bin/libmySQL.dll -Ld:/RC
ompile/CRANpkg/extralibs64/local/lib/x64 -Ld:/RCompile/CRANpkg/extralibs64/local/lib -LC:/PROGRA~1/R/R-30~1.1/bin/x64 -l
R
installing to C:/Program Files/R/R-3.0.1/library/RMySQL/libs/x64
** R
** inst
** preparing package for lazy loading
Creating a generic function for 'format' from package 'base' in package 'RMySQL'
Creating a generic function for 'print' from package 'base' in package 'RMySQL'
** help
*** installing help indices
** building package indices
** installing vignettes
** testing if installed package can be loaded
MYSQL_HOME defined as D:\toolkit\mysql56
* DONE (RMySQL)
```

安装成功！

### 在MySQL中建库建表

```{bash}
~ mysql -uroot -p

mysql> create database rmysql;
Query OK, 1 row affected (0.04 sec)

mysql> grant all on rmysql.* to rmysql@'%' identified by 'rmysql';
Query OK, 0 rows affected (0.00 sec)

mysql> grant all on rmysql.* to rmysql@localhost identified by 'rmysql';
Query OK, 0 rows affected (0.00 sec)

mysql> use rmysql
Database changed
mysql> CREATE TABLE t_user(
    -> id INT PRIMARY KEY AUTO_INCREMENT,
    -> user varchar(12) NOT NULL UNIQUE
    -> )ENGINE=INNODB DEFAULT CHARSET=utf8;
Query OK, 0 rows affected (1.01 sec)

mysql>
mysql> INSERT INTO t_user(user) values('A1'),('AB'),('fens.me');
Query OK, 3 rows affected (0.05 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM t_user;
+----+---------+
| id | user    |
+----+---------+
|  1 | A1      |
|  2 | AB      |
|  3 | fens.me |
+----+---------+
3 rows in set (0.03 sec)
```

### 通过R程序，读MySQL数据库数据。

注：如果刚才没有把MYSQL_HOME的变量写到环境变更中，每次在启动R之前要，先设置变量。

```{bash}
~ set MYSQL_HOME=D:\toolkit\mysql56
~ R

> library(RMySQL)
DBI
MYSQL_HOME defined as D:\toolkit\mysql56

> conn  users = dbGetQuery(conn, "SELECT * FROM t_user")
> dbDisconnect(conn)
[1] TRUE
> users
  id    user
1  1      A1
2  2      AB
3  3 fens.me
```

好了，我们实现了在Win7下R和MySQL的连接。

## 4. RMySQL函数使用

环境都安装好了，接下来我们具体使用一下RMySQL的包。

* RMySQL辅助操作
* RMySQL数据库操作
* 针对win的字符集设置

### 1). RMySQL辅助操作

加载类库

```{bash}
> library(RMySQL)
```

建立本地连接

```{bash}
> conn <- dbConnect(MySQL(), dbname = "rmysql", username="rmysql", password="rmysql",client.flag=CLIENT_MULTI_STATEMENTS)
```

建立远程连接

```{bash}
> conn <- dbConnect(MySQL(), dbname = "rmysql", username="rmysql", password="rmysql",host="192.168.1.201",port=3306)
```

关闭连接

```{bash}
dbDisconnect(conn)
```

查看数据库的表

```{bash}
> dbListTables(conn)
[1] "t_user"
```

查看表的字段

```{bash}
> dbListFields(conn, "t_user")
[1] "id"   "user"
```

查询MySQL信息

```{bash}
> summary(MySQL(), verbose = TRUE)
<MySQLDriver:(23864)> 
  Driver name:  MySQL 
  Max  connections: 16 
  Conn. processed: 3 
  Default records per fetch: 500 
  DBI API version:  

# MySQL连接实例信息
> summary(conn, verbose = TRUE)
<MySQLConnection:(23864,2)> 
  User: root 
  Host: localhost 
  Dbname: rmysql 
  Connection type: localhost via TCP/IP 
  MySQL server version:  5.6.11 
  MySQL client version:  5.6.11 
  MySQL protocol version:  10 
  MySQL server thread id:  35 
  No resultSet available

# MySQL连接信息
> dbListConnections(MySQL())
[[1]]
<MySQLConnection:(23864,2)> 
```

## 2). RMySQL数据库操作

RMySQL数据库操作

```{bash}
# 建表并插入数据
> t_demo<-data.frame(
  a=seq(1:10),
  b=letters[1:10],
  c=rnorm(10)
)
> dbWriteTable(conn, "t_demo", t_demo)

# 获得整个表数据
> dbReadTable(conn, "t_demo")
    a b           c
1   1 a  0.98868164
2   2 b -0.66935770
3   3 c  0.27703638
4   4 d  1.36137156
5   5 e -0.70291017
6   6 f  1.61235088
7   7 g  0.17616068
8   8 h  0.29700017
9   9 i  0.19032719
10 10 j -0.06222173

# 插入新数据
> dbWriteTable(conn, "t_demo", t_demo, append=TRUE)
> dbReadTable(conn, "t_demo")
   row_names  a b           c
1          1  1 a  0.98868164
2          2  2 b -0.66935770
3          3  3 c  0.27703638
4          4  4 d  1.36137156
5          5  5 e -0.70291017
6          6  6 f  1.61235088
7          7  7 g  0.17616068
8          8  8 h  0.29700017
9          9  9 i  0.19032719
10        10 10 j -0.06222173
11         1  1 a  0.98868164
12         2  2 b -0.66935770
13         3  3 c  0.27703638
14         4  4 d  1.36137156
15         5  5 e -0.70291017
16         6  6 f  1.61235088
17         7  7 g  0.17616068
18         8  8 h  0.29700017
19         9  9 i  0.19032719
20        10 10 j -0.06222173

# 覆盖原表数据
> dbWriteTable(conn, "t_demo", t_demo, overwrite=TRUE)

# 1). 查询数据
> d0 <- dbGetQuery(conn, "SELECT * FROM t_demo where c>0")
> class(d0)
[1] "data.frame"

> d0
  row_names a b         c
1         1 1 a 0.9886816
2         3 3 c 0.2770364
3         4 4 d 1.3613716
4         6 6 f 1.6123509
5         7 7 g 0.1761607
6         8 8 h 0.2970002
7         9 9 i 0.1903272

# 2). 执行SQL脚本查询，并分页
> rs <- dbSendQuery(conn, "SELECT * FROM t_demo where c>0")
> class(rs)
[1] "MySQLResult"
attr(,"package")
[1] "RMySQL"
> mysqlCloseResult(rs)
[1] TRUE

> d1 <- fetch(rs, n = 3)
> d1
  row_names a b         c
1         1 1 a 0.9886816
2         3 3 c 0.2770364
3         4 4 d 1.3613716

# 3). 查看集统计信息
> summary(rs, verbose = TRUE)
  row_names               a              b                   c         
 Length:7           Min.   :1.000   Length:7           Min.   :0.1762  
 Class :character   1st Qu.:3.500   Class :character   1st Qu.:0.2337  
 Mode  :character   Median :6.000   Mode  :character   Median :0.2970  
                    Mean   :5.429                      Mean   :0.7004  
                    3rd Qu.:7.500                      3rd Qu.:1.1750  
                    Max.   :9.000                      Max.   :1.6124

# 不插入row.names字段
> dbWriteTable(conn, "t_demo", t_demo,row.names=FALSE,overwrite=TRUE)
> dbGetQuery(conn, "SELECT * FROM t_demo where c>0")
  a b         c
1 1 a 0.9886816
2 3 c 0.2770364
3 4 d 1.3613716
4 6 f 1.6123509
5 7 g 0.1761607
6 8 h 0.2970002
7 9 i 0.1903272

# 删除表
> if(dbExistsTable(conn,'t_demo')){
+     dbRemoveTable(conn, "t_demo")
+ }
[1] TRUE
```

执行SQL语句，dbSendQuery

```{bash}
> query<-dbSendQuery(conn, "show tables")
> data <- fetch(query, n = -1)
> data
  Tables_in_rmysql
1           t_demo
2           t_user
> mysqlCloseResult(query)
[1] TRUE
```

## 3). win的字符集设置

在win7中，向MySQL插入中文

```{bash}
mysql> INSERT INTO t_user(user) values('小朋友'),('你好'),('正确了');
Query OK, 3 rows affected (0.07 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> select * from t_user;
+----+---------+
| id | user    |
+----+---------+
|  1 | A1      |
|  2 | AB      |
|  3 | fens.me |
|  5 | 你好    |
|  4 | 小朋友  |
|  6 | 正确了  |
+----+---------+
6 rows in set (0.07 sec)
```

通过RMySQL查询

```{bash}
> dbGetQuery(conn, "SELECT * FROM t_user")
  id    user
1  1      A1
2  2      AB
3  3 fens.me
4  5      ??
5  4     ???
6  6     ???
```

设置GKB字符集

```{bash}
> dbDisconnect(conn)
> conn <- dbConnect(MySQL(), dbname = "rmysql", username="root", password="",client.flag=CLIENT_MULTI_STATEMENTS)
> dbSendQuery(conn,'SET NAMES gbk')
 
> query<-dbSendQuery(conn, "SELECT * FROM t_user")
> data <- fetch(query, n = -1)
> mysqlCloseResult(query)
[1] TRUE
> data
  id    user
1  1      A1
2  2      AB
3  3 fens.me
4  5    你好
5  4  小朋友
6  6  正确了
```

OK，我们在win下面修正字符编号的问题。

## 5. RMySQL案例实践

系统需求描述：Linux MySQL，Win7的R环境，远程连接

1. 通过SQL新建表t_blog，主键索引，唯一键索引
2. 用RMySQL插入数据，包括中文字段
3. 再用RMySQL取出数据

### 1). 通过SQL新建表t_blog，主键索引，唯一键索引

建表语句

```{bash}
CREATE TABLE t_blog(
id INT PRIMARY KEY AUTO_INCREMENT,
title varchar(12) NOT NULL UNIQUE,
author varchar(12) NOT NULL, 
length int NOT NULL,
create_date timestamp NOT NULL DEFAULT now()
)ENGINE=INNODB DEFAULT CHARSET=UTF8;

mysql> desc t_blog;
+-------------+-------------+------+-----+-------------------+----------------+
| Field       | Type        | Null | Key | Default           | Extra          |
+-------------+-------------+------+-----+-------------------+----------------+
| id          | int(11)     | NO   | PRI | NULL              | auto_increment |
| title       | varchar(12) | NO   | UNI | NULL              |                |
| author      | varchar(12) | NO   |     | NULL              |                |
| length      | int(11)     | NO   |     | NULL              |                |
| create_date | timestamp   | NO   |     | CURRENT_TIMESTAMP |                |
+-------------+-------------+------+-----+-------------------+----------------+
5 rows in set (0.00 sec)

mysql> show indexes from t_blog;
+--------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table  | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+--------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| t_blog |          0 | PRIMARY  |            1 | id          | A         |           3 |     NULL | NULL   |      | BTREE      |         |               |
| t_blog |          0 | title    |            1 | title       | A         |           3 |     NULL | NULL   |      | BTREE      |         |               |
+--------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
2 rows in set (0.00 sec)


INSERT INTO t_blog(title,author,length) values('你好，第一篇','Conan',20),('RMySQL数据库编程','Conan',99),('R的极客理想系列文章','Conan',15);

mysql> select * from t_blog;
+----+------------------------------+--------+--------+---------------------+
| id | title                        | author | length | create_date         |
+----+------------------------------+--------+--------+---------------------+
|  1 | 你好，第一篇                 | Conan  |     20 | 2013-08-15 00:13:13 |
|  2 | RMySQL数据库编程             | Conan  |     99 | 2013-08-15 00:13:13 |
|  3 | R的极客理想系列文章          | Conan  |     15 | 2013-08-15 00:13:13 |
+----+------------------------------+--------+--------+---------------------+
3 rows in set (0.00 sec)
```

### 2). 用RMySQL插入数据，包括中文字段，再取出数据

```{bash}
> library(RMySQL)
> conn <- dbConnect(MySQL(), dbname = "rmysql", username="rmysql", password="rmysql",host="192.168.1.201",port=3306)
> 
> dbSendQuery(conn,'SET NAMES gbk')
 
> dbSendQuery(conn,"INSERT INTO t_blog(title,author,length) values('R插入的新文章','Conan',50)");
 
> 
> query<-dbSendQuery(conn, "SELECT * FROM t_blog")
Warning message:
In mysqlExecStatement(conn, statement, ...) :
  RS-DBI driver warning: (unrecognized MySQL field type 7 in column 4 imported as character)
> data <- fetch(query, n = -1)
> mysqlCloseResult(query)
[1] TRUE
> print(data)
  id               title author length         create_date
1  1        你好，第一篇  Conan     20 2013-08-15 00:13:13
2  2    RMySQL数据库编程  Conan     99 2013-08-15 00:13:13
3  3 R的极客理想系列文章  Conan     15 2013-08-15 00:13:13
4  4       R插入的新文章  Conan     50 2013-08-15 00:29:45
> 
> dbDisconnect(conn)
[1] TRUE
```

**特别提示，不能用dbWriteTable函数！！**

我们已经完成，掌握了RMySQL的各种使用技巧，希望大家理解原理后，能少犯错误，提高工作效率！

#### 转载请注明出处：http://blog.fens.me/r-mysql-rmysql/

