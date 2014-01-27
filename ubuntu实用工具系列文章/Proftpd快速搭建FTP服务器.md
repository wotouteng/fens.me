Proftpd快速搭建FTP服务器
==============

[ubuntu实用工具系列文章](http://blog.fens.me/series-ubuntu/)，将介绍基于Linux ubuntu的各种工具软件的配置和使用。有些工具大家早已耳熟能详，有些工具经常用到但确依然陌生。我将记录我在使用操作系统时，安装及配置工具上面的一些方法，把使用心得记录下来也便于自己的以后查找和回忆。

#### 关于作者
+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/linux-ftp-proftpd/

![Proftpd快速搭建FTP服务器](http://blog.fens.me/wp-content/uploads/2014/01/ftp-proftpd.png)

#### 前言

在Linux系统中，FTP服务器软件有很多，都已经成熟，像vsftpd, wu-ftp, Pure-FTPd等。但这些软件安装配置起来都比较麻烦，搭建个人的FTP服务器，还是Proftpd比较简单。

#### 目录

1. Proftpd介绍
2. Proftpd安装
3. Proftpd配置文件
4. Proftpd使用场景
5. 客户端访问

## 1. Proftpd介绍

Proftpd是一款开放源码的FTP服务器软件，它是原来世界范围使用最广泛的wu-ftpd的改进版，它修正了wu-ftpd的许多缺陷，在许多方面进行了重大的改进，其中一个重要变化就是它学习了Apache 的配置方式，使proftpd的配置和管理更加简单易懂。

有两种运行方式，独立服务器与超级服务器的子服务器。无论从安全性和稳定性，还是可配置性来说都是非常好的选择。

官方网站：http://www.proftpd.org/

## 2. Proftpd安装

### 系统环境

* Linux Ubuntu 12.04.2 LTS 64bit server

### 安装Proftpd

```{bash}
~ sudo apt-get install proftpd
<h
```

![](http://blog.fens.me/wp-content/uploads/2014/01/proftpd-install.png)


选择“standalone”

查看proftpd状态

```{bash}
~ sudo /etc/init.d/proftpd status
ProFTPD is started in standalone mode, currently running.

~ ps -aux|grep ftp
proftpd   6674  0.0  0.1  94648  2092 ?        Ss   16:05   0:00 proftpd: (accepting connections)

#启动端口21
~ netstat -nltp|grep 21
tcp6       0      0 :::21                   :::*                    LISTEN      -
```

## 3. Proftpd配置文件

配置文件：/etc/proftpd/proftpd.conf

```{bash}

#配置服务器名
ServerName			""blog.fens.me FTP Server" 

#设置服务器运行模式，独立服务，或者被监管
ServerType			standalone 

#设置为默认服务器
DefaultServer			on 

#设置服务器进程运行使用的用户
User				proftpd

#设置服务器进程运行使用的组
Group			        nogroup

#设置关闭IPv6支持
UseIPv6			        off

#设置服务器接受请求的端口
Port				21 

#设置被动模式使用的端口范围
PassivePorts 			60000 65535

#设置用户上传文件的权限掩码
Umask				022

#设置用户被chroot锁定到的各自的Home目录
DefaultRoot			/ftp

#关闭欢迎信息显示
DeferWelcome			off 

#如果显示欢迎信息，则指定显示的文件
DisplayLogin			welcome.msg 

#指定切换文件夹时，显示的欢迎信息
DisplayChdir			.message 

#设置日志
SystemLog                       /var/log/proftp.log
TransferLog                     /var/log/proftp-transfer.log
```

#限定操作

```{bash}
<Limit SITE_CHMOD>
DenyAll
</Limit>
```

#设置匿名用户资源

```{bash}
<Anonymous "/ftp/c">
...
</Anonymous>
```

#配置存储目录权限

```{bash}
<Directory "/ftp/a/" >
<Limit ALL>
AllowUser x
AllowUser a1
DenyAll
</Limit>

<Limit CWD READ RETR DIRS>
AllowAll
</Limit>
</Directory>
```

Limit权限说明：

* CWD ： Change Working Directory 进入该目录
* MKD ： Make Directory 创建目录
* RNFR ： Rename from 更名
* DELE ： Delete 删除文件
* RMD ： Remove Directory 删除目录
* READ ： 可读
* WRITE： 可写
* STOR ： 可上传
* RETR ： 可下载
* DIRS ： 允许列出目录
* LOGIN： 允许登录
* ALL ： 全部

## 4. Proftpd使用场景

场景描述：某公司建立统一的FTP服务器(/ftp)，公司有a部门(/ftp/a)，b两个部门(/ftp/b)，有独立的存储空间。

a1为a部门经理有对(/ftp/a)操作权限，a2为a部门员工只能读文件。

b1为b部门经理有对(/ftp/b)操作权限，b2为b部门员工只能读文件。

x为公司总经理，有对(/ftp)操作权限。

操作权限：

```{bash}
/ftp    -- x所有操作
/ftp/a  -- a1所有操作, a2只读
/ftp/b  -- b1所有操作, b2只读
```

命令操作:

```{bash}
# 创建FTP目录
sudo mkdir /ftp
sudo mkdir /ftp/a
sudo mkdir /ftp/b

# 创建用户组
sudo groupadd a
sudo groupadd b
```

Ubuntu系统配置nologin，与其他Linux稍有不同

```{bash}

~  sudo vi /etc/shells

# /etc/shells: valid login shells
/bin/sh
/bin/dash
/bin/bash
/bin/rbash
/usr/bin/tmux
/usr/bin/screen
/usr/sbin/nologin
```

最后一行增加/usr/sbin/nologin

创建用户并设置nologin, 禁止FTP账户登陆

```{bash}
sudo useradd a1 -g a -s /usr/sbin/nologin
sudo useradd a2 -g a -s /usr/sbin/nologin
sudo useradd b1 -g b -s /usr/sbin/nologin
sudo useradd b2 -g b -s /usr/sbin/nologin
sudo useradd x -G a,b -s /usr/sbin/nologin

#设置账户密码
echo -e "a1:123" | sudo chpasswd
echo -e "a2:123" | sudo chpasswd
echo -e "b1:123" | sudo chpasswd
echo -e "b1:123" | sudo chpasswd
echo -e "x:123" | sudo chpasswd

#设置目录权限
sudo chown x /ftp
sudo chown a1:a /ftp/a
sudo chmod 770 /ftp/a
sudo chmod g+s /ftp/a
sudo chown b1:b /ftp/b
sudo chmod 770 /ftp/b
sudo chmod g+s /ftp/b
```

修改配置文件：/etc/proftpd/proftpd.conf

```{bash}

~ sudo vi /etc/proftpd/proftpd.conf

ServerName "blog.fens.me FTP Server"
ServerType standalone
DefaultServer on
Port 21
Umask 022
MaxInstances 30
User proftpd
Group nogroup
DefaultRoot /ftp
AllowOverwrite on

SystemLog /var/log/proftp.log
TransferLog /var/log/proftp-transfer.log

<Directory "/ftp/*">
<Limit CWD READ>
AllowAll
</Limit>
</Directory>

<Directory "/ftp/a/" >
<Limit ALL>
AllowUser x
AllowUser a1
DenyAll
</Limit>

<Limit CWD READ RETR DIRS>
AllowAll
</Limit>
</Directory>

<Directory "/ftp/b/" >
<Limit ALL>
AllowUser x
AllowUser b1
DenyAll
</Limit>

<Limit CWD READ RETR DIRS>
AllowAll
</Limit>
</Directory>

<Limit SITE_CHMOD>
DenyAll
</Limit>
```

重新服务器

```{bash}
~ sudo /etc/init.d/proftpd restart
 * Stopping ftp server proftpd        [ OK ]
 * Starting ftp server proftpd 
```

## 5. 客户端访问

客户端命令行：Win7 64bit Command

模拟a1用户登陆：

```{bash}

~ ftp

#建立连接
ftp> open 192.168.1.201
连接到 192.168.1.201。
220 ProFTPD 1.3.4a Server (blog.fens.me FTP Server) [::ffff:192.168.1.201]
用户(192.168.1.201:(none)): a1
331 Password required for a1
密码:
230 User a1 logged in

#查看目录 
ftp> dir
200 PORT command successful
150 Opening ASCII mode data connection for file list
drwxrws---   2 a1       a            4096 Nov  3 12:59 a
drwxrws---   2 b1       b            4096 Nov  3 12:25 b
226 Transfer complete
ftp: 收到 116 字节，用时 0.00秒 58.00千字节/秒。

ftp> cd a
250 CWD command successful

ftp> pwd
257 "/a" is the current directory

ftp> dir
200 PORT command successful
150 Opening ASCII mode data connection for file list
-rw-r--r--   1 a2       a           55723 Nov  3 12:56 36kryunjiasu.docx
-rw-r--r--   1 a1       a               4 Nov  3 12:24 test.txt
226 Transfer complete
ftp: 收到 139 字节，用时 0.00秒 69.50千字节/秒。

#上传文件
ftp> put c:\22.log
200 PORT command successful
150 Opening ASCII mode data connection for 22.log
226 Transfer complete
ftp: 发送 120 字节，用时 0.06秒 2.18千字节/秒。

#下载文件
ftp> get test.txt
200 PORT command successful
150 Opening ASCII mode data connection for test.txt (4 bytes)
226 Transfer complete
ftp: 收到 5 字节，用时 0.00秒 5000.00千字节/秒。

#尝试访问B目录，出错
ftp> cd ../b
550 ../b: No such file or directory

#退出
ftp> bye
221 Goodbye.
```

模拟a2用户登陆：

```{bash}

~ ftp

#建立连接
ftp> open 192.168.1.201
连接到 192.168.1.201。
220 ProFTPD 1.3.4a Server (blog.fens.me FTP Server) [::ffff:192.168.1.201]
用户(192.168.1.201:(none)): a2
331 Password required for a2
密码:
230 User a2 logged in

#查看目录 
ftp> dir
200 PORT command successful
150 Opening ASCII mode data connection for file list
drwxrws---   2 a1       a            4096 Nov  3 13:09 a
drwxrws---   2 b1       b            4096 Nov  3 12:25 b
226 Transfer complete
ftp: 收到 116 字节，用时 0.00秒 116.00千字节/秒。

#上传文件，出错
ftp> put c:\11.log
200 PORT command successful
550 11.log: Operation not permitted

#下载文件
ftp> get 22.log
200 PORT command successful
150 Opening ASCII mode data connection for 22.log (114 bytes)
226 Transfer complete
ftp: 收到 120 字节，用时 0.00秒 120000.00千字节/秒。
```

用Proftpd我们快速建立了一个公司内部的FTP服务器，比wu-ftpd要方便。

#### 转载请注明出处：http://blog.fens.me/linux-ftp-proftpd/

