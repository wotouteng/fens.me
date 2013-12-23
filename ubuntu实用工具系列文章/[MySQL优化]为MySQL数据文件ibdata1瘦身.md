[MySQL优化]为MySQL数据文件ibdata1瘦身
================

[ubuntu实用工具系列文章](http://blog.fens.me/series-ubuntu/)，将介绍基于Linux ubuntu的各种工具软件的配置和使用。有些工具大家早已耳熟能详，有些工具经常用到但确依然陌生。我将记录我在使用操作系统时，安装及配置工具上面的一些方法，把使用心得记录下来也便于自己的以后查找和回忆。

#### 关于作者
+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/mysql-ibdata1/

![为MySQL数据文件ibdata1瘦身](http://blog.fens.me/wp-content/uploads/2013/08/mysql-ibdata1.png)

#### 前言

MySQL在运行一段时间后，ibdata1的文件会增长大小，就算删除了表的数据，ibdata1的体积也不会减小。由于硬盘空间有限，这样一直膨胀下去磁盘空间接近崩溃。今天在导出数据的时候就发现了，磁盘竟然满了，明明预留了1个月的用量，1周就占满了,下面就要给ibdata1做个瘦身。

#### 目录

1. 系统环境
2. 发现问题
3. 解决问题

## 1. 系统环境

Linux Ubuntu 13.04 64bit server

```{bash}
~ uname -a
Linux d2 3.8.0-21-generic #32-Ubuntu SMP Tue May 14 22:16:46 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux
~ cat /etc/issue
Ubuntu 13.04 \n \l
```

MySQL: 5.5.31-0ubuntu0.13.04.1

```{bash}
~ mysql --version
mysql  Ver 14.14 Distrib 5.5.31, for debian-linux-gnu (x86_64) using readline 6.2
```

硬盘：36G+4G+4G+36G

```{bash}
~ df -h
Filesystem                    Size  Used Avail Use% Mounted on
/dev/mapper/server3--vg-root   36G   31G  3.2G  91% /
none                          4.0K     0  4.0K   0% /sys/fs/cgroup
udev                          4.1G  1.1G  3.0G  26% /dev
tmpfs                         824M  280K  823M   1% /run
none                          5.0M     0  5.0M   0% /run/lock
none                          4.1G  3.4G  729M  83% /run/shm
none                          100M     0  100M   0% /run/user
/dev/vda1                     228M   30M  187M  14% /boot
192.168.1.10:/home/amg/data    36G   13G   21G  39% /home/amg/data
```

MySQL的ibdata1占用空间:20G

```{bash}

~ cd /var/lib/mysql
~ ls -l
drwxr-xr-x 2 mysql mysql        4096 Aug  2 19:38 CB
drwxr-xr-x 2 mysql mysql        4096 Jun 24 23:08 conan
drwxr-xr-x 2 mysql mysql        4096 Jun  2 00:52 dbwordpress
-rwxr-xr-x 1 root  root            0 May 23 00:48 debian-5.5.flag
-rwxr-xr-x 1 mysql mysql 20101201920 Aug  2 20:08 ibdata1
-rwxr-xr-x 1 mysql mysql     5242880 Aug  2 20:08 ib_logfile0
-rwxr-xr-x 1 mysql mysql     5242880 Aug  2 19:38 ib_logfile1
drwxr-xr-x 2 mysql mysql        4096 Jun 26 09:03 Macro
drwxr-xr-x 2 mysql root         4096 May 23 00:48 mysql
-rwxr-xr-x 1 root  root            6 May 23 00:48 mysql_upgrade_info
drwxr-xr-x 2 mysql mysql        4096 May 23 00:48 performance_schema
drwxr-xr-x 2 mysql mysql        4096 May 23 00:53 phpmyadmin
drwxr-xr-x 2 mysql root         4096 May 23 00:48 test
drwxr-xr-x 2 mysql mysql        4096 Jul 22 14:09 TF
drwxr-xr-x 2 mysql mysql        4096 Jun  2 01:04 wordpress
```

业务数据表

```{bash}
mysql> show tables;
+-----------------+
| Tables_in_CB    |
+-----------------+
| NSpremium       |
| cb_hft          |
| cb_hft_20130801 |
| cb_hft_20130802 |
+-----------------+
4 rows in set (0.00 sec)
```

## 2. 发现问题

ibdata1单个文件占用20G大小。

1). MySQL默认设置，没有按表空间分离数据，所有的表的数据都被放到ibdata1文件中。
2). 业务操作，每天会产生一张表cb_hft，晚上的时候对表进行重命名。

```{bash}
RENAME TABLE cb_hft TO cb_hft_20130801;
create table cb_hft like cb_hft_20130801;
```

3). 每周会把数据导出，同时drop表。但drop后，ibdata1不会减少，随着数据的积累ibdata1越来越大，根空间已经不够用了。

## 3. 解决问题

### 1). 导出数据
现在数据库中，有两个数据表，cb_hft_20130801,cb_hft_20130802，分别导出到/run/shm, /dev

```{bash}

~ cd /dev
~ mysqldump -uroot -p CB cb_hft_20130802 > export_cb_hft_20130802.sql

~ cd /run/shm
~ mysqldump -uroot -p CB cb_hft_20130801 > export_cb_hft_20130801.sql

~ df -h
Filesystem                    Size  Used Avail Use% Mounted on
/dev/mapper/server3--vg-root   36G   31G  3.2G  91% /
none                          4.0K     0  4.0K   0% /sys/fs/cgroup
udev                          4.1G  3.7G  368M  92% /dev
tmpfs                         824M  280K  823M   1% /run
none                          5.0M     0  5.0M   0% /run/lock
none                          4.1G  3.4G  729M  83% /run/shm
none                          100M     0  100M   0% /run/user
/dev/vda1                     228M   30M  187M  14% /boot
192.168.1.10:/home/amg/data    36G   13G   21G  39% /home/amg/data
```

两张表分别占了，3.4G，3.7G。

登陆mysql删除CB数据库

```{bash}
~ mysql -uroot -p
~ drop database CB
```

导出其他数据库数据

```{bash}
~ cd /run/shm
~ mysqldump -uroot -p -R -q --all-databases > others.sql
```

### 2). 修改配置文件/etc/mysql/my.cnf
对每张表使用单独的数据文件存储innodb_file_per_table

停止mysql服务器

```{bash}
~ sudo /etc/init.d/mysql stop

#清空所有数据文件
~ sudo rm -rf /var/lib/mysql/*
```

修改配置文件

```{bash}
~ sudo vi /etc/mysql/my.cnf

[mysqld]
innodb_file_per_table
```

重新构建数据库实例

```{bash}
~ /usr/bin/mysql_install_db

~ ls /var/lib/mysql
mysql  performance_schema  test

#启动MySQL
~ sudo /etc/init.d/mysql start
```

### 3). 恢复其他数据库

```{bash}

~ mysql < /run/shm/others.sql 
~ mysql -umysql -p 

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| Macro              |
| TF                 |
| conan              |
| dbwordpress        |
| mysql              |
| performance_schema |
| phpmyadmin         |
| test               |
| wordpress          |
+--------------------+
10 rows in set (0.01 sec)

#查看ibdata1大小
~ ls -l /var/lib/mysql
drwx------ 2 mysql mysql     4096 Aug  2 21:33 CB
drwx------ 2 mysql mysql     4096 Aug  2 21:23 conan
drwx------ 2 mysql mysql     4096 Aug  2 21:23 dbwordpress
-rw-rw---- 1 mysql mysql 18874368 Aug  2 21:34 ibdata1
-rw-rw---- 1 mysql mysql  5242880 Aug  2 21:34 ib_logfile0
-rw-rw---- 1 mysql mysql  5242880 Aug  2 21:34 ib_logfile1
drwx------ 2 mysql mysql     4096 Aug  2 21:23 Macro
drwx------ 2 mysql root      4096 Aug  2 21:23 mysql
drwx------ 2 mysql mysql     4096 Aug  2 21:19 performance_schema
drwx------ 2 mysql mysql     4096 Aug  2 21:23 phpmyadmin
drwx------ 2 mysql root      4096 Aug  2 21:19 test
drwx------ 2 mysql mysql     4096 Aug  2 21:23 TF
drwx------ 2 mysql mysql     4096 Aug  2 21:23 wordpress
```

### 4). 重置root密码

```{bash}
mysql> SET PASSWORD FOR 'root'@'localhost' = PASSWORD('123456');
mysql> FLUSH PRIVILEGES;
```

### 5). 恢复CB数据库

```{bash}

mysql> create database CB;
Query OK, 1 row affected (0.00 sec)

~  mysql --database CB < /run/shm/export_cb_hft_20130801.sql
~  mysql --database CB < /dev/export_cb_hft_20130802.sql
~  mysql --database CB < /dev/export_NSpremium.sql

#查看ibdata1大小：还是出初始值没有增长
~ ls -l /var/lib/mysql
drwx------ 2 mysql mysql     4096 Aug  2 21:33 CB
drwx------ 2 mysql mysql     4096 Aug  2 21:23 conan
drwx------ 2 mysql mysql     4096 Aug  2 21:23 dbwordpress
-rw-rw---- 1 mysql mysql 18874368 Aug  2 22:01 ibdata1
-rw-rw---- 1 mysql mysql  5242880 Aug  2 22:01 ib_logfile0
-rw-rw---- 1 mysql mysql  5242880 Aug  2 22:01 ib_logfile1
drwx------ 2 mysql mysql     4096 Aug  2 21:23 Macro
drwx------ 2 mysql root      4096 Aug  2 21:23 mysql
drwx------ 2 mysql mysql     4096 Aug  2 21:19 performance_schema
drwx------ 2 mysql mysql     4096 Aug  2 21:23 phpmyadmin
drwx------ 2 mysql root      4096 Aug  2 21:19 test
drwx------ 2 mysql mysql     4096 Aug  2 21:23 TF
drwx------ 2 mysql mysql     4096 Aug  2 21:23 wordpress

#查看CB库目录：所有的数据都保存在自己单独的数据文件
~ ls -l /var/lib/mysql/CB
-rw-rw---- 1 mysql mysql       9928 Aug  2 21:33 cb_hft_20130801.frm
-rw-rw---- 1 mysql mysql 7159676928 Aug  2 22:08 cb_hft_20130801.ibd
-rw-rw---- 1 mysql mysql       9928 Aug  2 22:09 cb_hft_20130802.frm
-rw-rw---- 1 mysql mysql 7805599744 Aug  2 22:38 cb_hft_20130802.ibd
-rw-rw---- 1 mysql mysql         61 Aug  2 21:30 db.opt
```

刚才设置的innodb_file_per_table参数已经起作用了，当我们再导出表drop后，对应的数据文件idb就会被删除，系统硬盘空间使用就会在正常值范围内。

查看表数据

```{bash}

mysql> show tables;
+-----------------+
| Tables_in_CB    |
+-----------------+
| cb_hft_20130801 |
| cb_hft_20130802 |
+-----------------+
2 rows in set (0.00 sec)

mysql> select count(1) from cb_hft_20130801;
+----------+
| count(1) |
+----------+
| 21063172 |
+----------+
1 row in set (1 min 1.46 sec)

#删除表
~ drop table cb_hft_20130801;

#查看数据文件
~ ls -l /var/lib/mysql/CB
-rw-rw---- 1 mysql mysql       9928 Aug  2 22:09 cb_hft_20130802.frm
-rw-rw---- 1 mysql mysql 7805599744 Aug  2 22:38 cb_hft_20130802.ibd
-rw-rw---- 1 mysql mysql         61 Aug  2 21:30 db.opt
-rw-rw---- 1 mysql mysql       9274 Aug  2 22:52 NSpremium.frm
-rw-rw---- 1 mysql mysql      98304 Aug  2 22:53 NSpremium.ibd
```

drop后，数据就一起被删除了。

经过对MySQL的调优，ibdata1已经被瘦身！数据库又可以继续正常的稳定的工作了。

#### 转载请注明出处：http://blog.fens.me/mysql-ibdata1/

