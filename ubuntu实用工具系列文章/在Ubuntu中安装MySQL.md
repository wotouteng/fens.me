在Ubuntu中安装MySQL
=============

[ubuntu实用工具系列文章](http://blog.fens.me/series-ubuntu/)，将介绍基于Linux ubuntu的各种工具软件的配置和使用。有些工具大家早已耳熟能详，有些工具经常用到但确依然陌生。我将记录我在使用操作系统时，安装及配置工具上面的一些方法，把使用心得记录下来也便于自己的以后查找和回忆。

#### 关于作者
+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/linux-mysql-install/

![在Ubuntu中安装MySQL](http://blog.fens.me/wp-content/uploads/2014/03/ubuntu-mysql-install.png)

#### 前言

安装MySQL是个老话题，我安装MySQL服务器已不下百次了，为了博客文章结构的连贯性，还是再写一篇做为环境基础，同时也给自己一个备忘。

#### 目录

1. MySQL在Windows中安装
2. MySQL在Linux Ubuntu中安装
3. 通过命令行客户端访问MySQL
4. 修改MySQL服务器的配置
5. 新建数据库并设置访问账号

## 1. MySQL在Windows中安装

在Windows系统上安装MySQl数据库是件非常简单的事情，下载压缩包，解压即可。下载地址：http://dev.mysql.com/downloads/mysql/

* MySQL服务器运行命令：MySQL安装目录/bin/mysqld.exe
* MySQL客户端运行命令：MySQL安装目录/bin/mysql.exe

## 2. MySQL在Linux Ubuntu中安装

本文使用的Linux是Ubuntu 12.04.2 LTS 64bit的系统，安装MySQL数据库软件包可以通过apt-get实现。

在Linux Ubuntu中安装MySQL数据库

```{bash}

#安装MySQL服务器端
~ sudo apt-get install mysql-server

```

安装过程会弹出提示框，输入root用户的密码，我在这里设置密码为mysql。

安装完成后，MySQL服务器会自动启动，我们检查MySQL服务器程序

```{bash}

# 检查MySQL服务器系统进程
~ ps -aux|grep mysql
mysql     3205  2.0  0.5 549896 44092 ?        Ssl  20:10   0:00 /usr/sbin/mysqld
conan     3360  0.0  0.0  11064   928 pts/0    S+   20:10   0:00 grep --color=auto mysql

# 检查MySQL服务器占用端口
~ netstat -nlt|grep 3306
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN

# 通过启动命令检查MySQL服务器状态
~ sudo /etc/init.d/mysql status
Rather than invoking init scripts through /etc/init.d, use the service(8)
utility, e.g. service mysql status

Since the script you are attempting to invoke has been converted to an
Upstart job, you may also use the status(8) utility, e.g. status mysql
mysql start/running, process 3205

# 通过系统服务命令检查MySQL服务器状态
~ service mysql status
mysql start/running, process 3205
```

## 3. 通过命令行客户端访问MySQL

安装MySQL服务器，会自动地一起安装MySQL命令行客户端程序。

在本机输入mysql命令就可以启动，客户端程序访问MySQL服务器。

```{bash}

~ mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 42
Server version: 5.5.35-0ubuntu0.12.04.2 (Ubuntu)

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

使用户名和密码，登陆服务器

```{bash}

~ mysql -uroot -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 37
Server version: 5.5.35-0ubuntu0.12.04.2 (Ubuntu)

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

MySQL的一些简单的命令操作。

```{bash}

# 查看所有的数据库
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| test               |
+--------------------+
2 rows in set (0.00 sec)

# 切换到information_schema库
mysql> use information_schema
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed

# 查看information_schema库中所有的表
mysql> show tables;
+---------------------------------------+
| Tables_in_information_schema          |
+---------------------------------------+
| CHARACTER_SETS                        |
| COLLATIONS                            |
| COLLATION_CHARACTER_SET_APPLICABILITY |
| COLUMNS                               |
| COLUMN_PRIVILEGES                     |
| ENGINES                               |
| EVENTS                                |
| FILES                                 |
| GLOBAL_STATUS                         |
| GLOBAL_VARIABLES                      |
| KEY_COLUMN_USAGE                      |
| PARAMETERS                            |
| PARTITIONS                            |
| PLUGINS                               |
| PROCESSLIST                           |
| PROFILING                             |
| REFERENTIAL_CONSTRAINTS               |
| ROUTINES                              |
| SCHEMATA                              |
| SCHEMA_PRIVILEGES                     |
| SESSION_STATUS                        |
| SESSION_VARIABLES                     |
| STATISTICS                            |
| TABLES                                |
| TABLESPACES                           |
| TABLE_CONSTRAINTS                     |
| TABLE_PRIVILEGES                      |
| TRIGGERS                              |
| USER_PRIVILEGES                       |
| VIEWS                                 |
| INNODB_BUFFER_PAGE                    |
| INNODB_TRX                            |
| INNODB_BUFFER_POOL_STATS              |
| INNODB_LOCK_WAITS                     |
| INNODB_CMPMEM                         |
| INNODB_CMP                            |
| INNODB_LOCKS                          |
| INNODB_CMPMEM_RESET                   |
| INNODB_CMP_RESET                      |
| INNODB_BUFFER_PAGE_LRU                |
+---------------------------------------+
40 rows in set (0.01 sec)

# 查看数据库的字符集编码
mysql> show variables like '%char%';
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | utf8                       |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | latin1                     |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
8 rows in set (0.00 sec)

```

## 4. 修改MySQL服务器的配置

接下来，我需要做一些配置，让MySQL符合基本的开发要求。

### 4.1 将字符编码设置为UTF-8

默认情况下，MySQL的字符集是latin1，因此在存储中文的时候，会出现乱码的情况，所以我们需要把字符集统一改成UTF-8。

用vi打开MySQL服务器的配置文件my.cnf

```{bash}

~ sudo vi /etc/mysql/my.cnf

#在[client]标签下，增加客户端的字符编码
[client]
default-character-set=utf8

#在[mysqld]标签下，增加服务器端的字符编码
[mysqld]
character-set-server=utf8
collation-server=utf8_general_ci
```

### 4.2 让MySQL服务器被远程访问

默认情况下，MySQL服务器不允许远程访问，只允许本机访问，所以我们需要设置打开远程访问的功能。

用vi打开MySQL服务器的配置文件my.cnf

```{bash}

~ sudo vi /etc/mysql/my.cnf

#注释bind-address
#bind-address            = 127.0.0.1
```

修改后，重启MySQL服务器。

```{bash}

~ sudo /etc/init.d/mysql restart
Rather than invoking init scripts through /etc/init.d, use the service(8)
utility, e.g. service mysql restart

Since the script you are attempting to invoke has been converted to an
Upstart job, you may also use the stop(8) and then start(8) utilities,
e.g. stop mysql ; start mysql. The restart(8) utility is also available.
mysql start/running, process 3577
```

重新登陆服务器

```{bash}

~ mysql -uroot -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 37
Server version: 5.5.35-0ubuntu0.12.04.2 (Ubuntu)

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

# 再次查看字符串编码
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

我们检查MySQL的网络监听端口

```{bash}
# 检查MySQL服务器占用端口
~ netstat -nlt|grep 3306
  tcp        0      0 0.0.0.0:3306            0.0.0.0:*               LISTEN

```

我们看到从之间的网络监听从 127.0.0.1:3306 变成 0 0.0.0.0:3306，表示MySQL已经允许远程登陆访问。通过root账号远程访问，是非常不安全的操作，因此我们下一步，将新建一个数据库，再新建一个用户进行远程访问。

## 5. 新建数据库并设置访问账号

通过root账号登陆MySQl服务器

```{bash}

~ mysql -uroot -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 39
Server version: 5.5.35-0ubuntu0.12.04.2 (Ubuntu)

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

# 新建数据库abc
mysql> CREATE DATABASE abc;

# 使用数据库abc
mysql> use abc;
Database changed

# 在数据库abc中，新建一张表a1
mysql> create table a1(id int primary key,name varchar(32) not null);
Query OK, 0 rows affected (0.05 sec)

# 新建book用户，密码为book，允许book可以远程访问abc数据库，授权book对abc进行所有数据库
mysql> GRANT ALL ON abc.* to book@'%' IDENTIFIED BY 'book';
Query OK, 0 rows affected (0.00 sec)

#允许book可以本地访问abc数据库，授权book对abc进行所有数据库
mysql> GRANT ALL ON abc.* to book@localhost IDENTIFIED BY 'book';
Query OK, 0 rows affected (0.00 sec)
```

我们在本地使用book用户登陆

```{bash}

# 使用book用户登陆
~ mysql -ubook -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 40
Server version: 5.5.35-0ubuntu0.12.04.2 (Ubuntu)

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

#进行abc数据库
mysql> use abc;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed

#查看abc数据库的表
mysql> show tables;
+---------------+
| Tables_in_abc |
+---------------+
| a1            |
+---------------+
1 row in set (0.00 sec)
```

我们在远程的另一台Linux使用book用户登陆

```{bash}

~ mysql -ubook -p -h 192.168.1.199
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 41
Server version: 5.5.35-0ubuntu0.12.04.2 (Ubuntu)

Copyright (c) 2000, 2012, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use abc
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+---------------+
| Tables_in_abc |
+---------------+
| a1            |
+---------------+
1 row in set (0.00 sec)
```

通过上面的操作，我们就把MySQL数据库服务器，在Linux Ubuntu中的系统安装完成。

#### 转载请注明出处：http://blog.fens.me/linux-mysql-install/





