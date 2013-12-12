RHadoop培训 之 Linux基础课
==================

[RHadoop实践系列文章](http://blog.fens.me/series-rhadoop/)，包含了R语言与Hadoop结合进行海量数据分析。Hadoop主要用来存储海量数据，R语言完成MapReduce 算法，用来替代Java的MapReduce实现。有了RHadoop可以让广大的R语言爱好者，有更强大的工具处理大数据1G, 10G, 100G, TB, PB。 由于大数据所带来的单机性能问题，可能会一去不复返了。

RHadoop实践是一套系列文章，主要包括[”Hadoop环境搭建”](http://blog.fens.me/rhadoop-hadoop/)，[”RHadoop安装与使用”](http://blog.fens.me/rhadoop-rhadoop/)，[”R实现MapReduce的协同过滤算法”](http://blog.fens.me/rhadoop-mapreduce-rmr/)，[”HBase和rhbase的安装与使用”](http://blog.fens.me/rhadoop-hbase-rhase/)。对于单独的R语言爱好者，Java爱好者，或者Hadoop爱好者来说，同时具备三种语言知识并不容 易。此文虽为入门文章，但R,Java,Hadoop基础知识还是需要大家提前掌握。

####关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

####转载请注明出处：  
http://blog.fens.me/rhadoop-linux-basic/

![](http://blog.fens.me/wp-content/uploads/2013/07/linux-basic.jpg)

####前言

覆盖Linux基础知识，快速上手，搭建RHadoop环境的基础课。

####目录

1. 背景知识  
2. 文件系统  
3. 常用命令  
4. vi编辑器  
5. 用户管理  
6. 磁盘管理  
7. 网络管理  
8. 系统管理  
9. 软件包管理  
10. 常用软件

##1. 背景知识

###Linux起源
Linux是一个诞生于网络、成长于网络且成熟于网络的奇特的操作系统。1991年，芬兰大学生Linus萌发了开发一个自由的UNIX操作系统的想法。

Ubuntu Linux最早于2004年作为Debian的一个分支出现，其创始人是南非企业家Mark Shuttleworth。Ubuntu项目由Shuttleworth的公司Canonical和社区志愿开发者共同努力开发而成，目的是实 现一个现代 版的Linux版本，使其在桌面系统上真正具有竞争力，更适合主流非技术用户使用。

Ubuntu的重点在于提高易用性，并且坚持定时发布新版本，即每隔六个月发布一个新版本。这确保了用户不再使用过时的软件。

http://www.ubuntu.com/

###Linux安装

+ 在物理上面安装Linux (略…)
+ 在虚拟机上面安装Linux

![](http://blog.fens.me/wp-content/uploads/2013/07/vbox-linux.png)

###客户端软件

Putty，http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

![](http://blog.fens.me/wp-content/uploads/2013/07/putty.png)

##2. 文件系统

###a.目录结构

```{bash}
ls /
bin dev home lib lost+found mnt proc run selinux sys usr vmlinuz
boot etc initrd.img lib64 media opt root sbin srv tmp var
```

###b.常用目录介绍

```{bash}
/bin 所有用户都能执行的命令
/dev 设备特殊文件
/etc 系统管理和配置文件
/home 用户主目录的基点
/sbin 系统管理员才能执行的命令
/tmp 公用的临时文件存储点
/root 系统管理员的主目录
/mnt 系统提供这个目录是让用户临时挂载其他的文件系统
/var 某些大文件的溢出区，比方说各种服务的日志文件
```

###c.简单了解的目录介绍

```{bash}
/lib 标准程序设计库
/lib64 标准程序设计库
/lost+found 这个目录平时是空的，系统非正常关机而留下恢复的文件
/proc 正在运行的内核信息映射
/sys 硬件设备的驱动程序信息
/usr 最庞大的目录，要用到的应用程序和文件几乎都在这个目录
/usr/bin 众多的应用程序
/boot 该目录默认下存放的是Linux的启动文件和内核
/media 挂载多媒体设备
/opt 第三方软件在安装时默认会找这个目录,所以你没有安装此类软件时它是空的
```

###d. 文件和目录

```{bash}
ls -l /etc/passwd
-rw-r--r-- 1 root root 1146 May 28 20:48 /etc/passwd
第一列：文件类型和权限
第二列：i节点，硬件连接数
第三列：用户
第四列：用户组
第五列：文件大小
第六列：最近一次修改时间mtime
第七列：文件或者目录名

上面第一列： -rw-r--r--
1列：文件类型 (-普通文件, d目录, l链接文件)
2-10列：用户权限，用户组权限，其他人权限, 读r(4)，写w(2)，执行x(1)
rw- : 4+2+0=6
r-- : 4+0+0=4
r-- : 4+0+0=4
chmod 644 /etc/passwd
```

##3. 常用命令

```{bash}
man: 查询帮助文档
ls: 列出目前下的所有内容 (a, l)
cd: 变换工作目录(~, . , .., -)
pwd: 显示当前目录
touch: 创建一个空文件或者改变创建时间
mkdir: 创建一个目录(p)
rmdir: 删除一个空目录(p)
rm: 删除文件和目录 (rf)
cp: 复制文件和目录(rf)
mv: 移动文件和目录
cat: 显示一个文件内容
head: 显示文件前N行 (-n)
tail: 显示文件后N行(-n)

less : 分屏显示文件内容
wc: 统计计数 (l,w,m)
chmod:  给目录设置权限(R)
chown:  修改目录和文件用户或者用户组 (R)
chgrp: 修改目录和文件用户组(R)
which: 按路径查找命令
whereis: 按资料库查找命令
locate: 按文件包查询
find: 按条件查询
gzip: 压缩和解压文件(-n,d)
zcat: 查看gzip压缩文件内容

zip: 以zip压缩文件和目录 (r)
unzip: 解压zip文件
bzip2: 以bz2压缩和解压文件 (z,d) 
bzcat: 查看bz2压缩文件内容
tar: 打包并压缩文件(czvf, xzvf)
shutdown: 定时关机(-h 18:00, -r重启)
halt: 关机
init: 切换运行级别(0关机)
poweroff: 关机
reboot: 重启
history: 查看命令行历史
grep: 使用正则表达式搜索文本

echo:  打印环境变量
export: 查看环境变量，或者声明变量
clear: 清空屏幕
alias: 设置别名(alias cls='clear')
locale: 当前系统语言环境
sudo: 切换成root用户运行
> : 输入到文件 (locale > a.txt)
>> : 合并到文件末尾 (locale >> a.txt)
| : 管道过滤 (cat a.txt| grep en_US)
ln: 建立链接文件(s软链接)
sh: 执行一个shell脚本
date: 查看当前系统时间
time：统计程序消耗时间
```

##4. vi编辑器

###a. vi的基本概念
基本上vi可分为三种操作状态，分别是命令模式(Command mode)、插入模式(Insert mode)和底线命令模式(Last line mode)，

+ Comand mode：控制屏幕光标的移动，字符或光标的删除，移动复制某区段及进入Insert mode下，或者到Last line mode。
+ Insert mode：唯有在Insert mode下，才可做文字数据输入，按Esc等可回到Comand mode。
+ Last line mode：将储存文件或离开编辑器，也可设置编辑环境，如寻找字符串、列出行号等。

###b. 命令模式(Command mode)

移动光标类命令:

```{bash}
h ：光标左移一个字符 
l ：光标右移一个字符 
space：光标右移一个字符 
Backspace：光标左移一个字符 
k或Ctrl+p：光标上移一行 
j或Ctrl+n ：光标下移一行 
Enter ：光标下移一行 
w或W ：光标右移一个字至字首 
b或B ：光标左移一个字至字首 
e或E ：光标右移一个字至字尾 
) ：光标移至句尾 
( ：光标移至句首 
}：光标移至段落开头 
{：光标移至段落结尾 
$：光标移至当前行尾 
```

屏幕翻滚类命令

```{bash}
Ctrl+u：向文件首翻半屏 
Ctrl+d：向文件尾翻半屏 
Ctrl+f：向文件尾翻一屏 
Ctrl+b；向文件首翻一屏  
```

插入文本类命令

```{bash}
i ：在光标前 
I ：在当前行首 
a：光标后 
A：在当前行尾 
o：在当前行之下新开一行 
O：在当前行之上新开一行 
```

删除命令

```{bash}
do：删至行首 
d$：删至行尾 
ndd：删除当前行及其后n-1行 
```

###c. 插入模式(Insert mode)  
编辑文本信息

###d. 底线命令模式(Last line mode)

```{bash}
：n1,n2 co n3：将n1行到n2行之间的内容拷贝到第n3行下 
：n1,n2 m n3：将n1行到n2行之间的内容移至到第n3行下 
：n1,n2 d ：将n1行到n2行之间的内容删除 
：w ：保存当前文件 
：e filename：打开文件filename进行编辑 
：x：保存当前文件并退出 
：q：退出vi 
：q!：不保存文件并退出vi 
：!command：执行shell命令command 
：n1,n2 w!command：将文件中n1行至n2行的内容作为command的输入并执行之，若不指定n1，n2，则表示将整个文件内容作为command的输入 
：r!command：将命令command的输出结果放到当前行 
```

##5. 用户管理

```{bash}

useradd：命令用来建立用户帐号和创建用户的起始目录。
passwd: 设定账号的密码
groupadd: 将新组加入系统
adduser: 增加用户到sudo

groupadd hadoop 
useradd hadoop -g hadoop;
passwd hadoop 
adduser hadoop sudo
mkdir /home/hadoop 
chown -R hadoop:hadoop /home/hadoop

sudo: 切换到root执行命令
  sudo –i: 切换到root账号

su: 切换其他用户      
who: 显示系统中有哪些用户登陆系统。
whoami: 显示当前操作的用户名

ls -l /etc/passwd
-rw-r--r-- 1 root root 1146 May 28 20:48 /etc/passwd
```

##6. 磁盘管理

```{bash}
fdisk: 硬盘分区工具(l)
partprobe: 不重启让分区生效
mkfs: 格式化硬盘(-t ext4)

mount: 挂载分区
umount: 取消挂载分区

/etc/fstab:开机自动挂载
    /dev/vdb1        /home/cos/hadoop      ext4    defaults 0       0

df: 查看当前硬盘的使用情况(ah)
du: 查看目录下的文件夹大小(shd)
```

##7. 网络管理

###a. 网络配置

```{bash}
hostname: 显示或修改主机名
/etc/hostname
    u1

/etc/hosts：配置主机名与ip的映射
    127.0.0.1       localhost
    127.0.1.1       u1

    # The following lines are desirable for IPv6 capable hosts
    ::1     ip6-localhost ip6-loopback
    fe00::0 ip6-localnet
    ff00::0 ip6-mcastprefix
    ff02::1 ip6-allnodes
    ff02::2 ip6-allrouters

/etc/network/interfaces: 配置网卡信息
    auto lo
    iface lo inet loopback

    auto eth0
    #iface eth0 inet dhcp
    iface eth0 inet static
    address 192.168.1.200
    netmask 255.255.255.0
    gateway 192.168.1.1

/etc/init.d/networking restart: 重启网络配置

ifup eth0: 激活eth0网卡
ifdown eth0: 关闭eth0网卡

/etc/resolv.conf: DNS配置
    nameserver 192.168.1.1
    nameserver 8.8.8.8
```

###b. 网络命令

```{bash}
ping: 检查主机网络接口状态
netstat: 检查整个Linux网络状态。
route: 检查本地路由信息
ifconfig: 检查网卡的信息
nslookup：机器的IP地址和其对应的域名
dig:用于询问DNS域名服务器的灵活的工具
traceroute：追踪网络数据包的路由途径
```

##8. 系统管理

###a. 系统命令

```{bash}
ps: 检查系统进程(aux)
kill: 指定进程ID杀进程(-9)
killall: 通过批量名字杀死进程(-9)
top: 查看系统运行状态，以CPU占用最高排序
free: 查看系统内存资源
```

###b. 系统日志

```{bash}
dmesg：dmesg用来显示开机信息，kernel会将开机信息存储在ring buffer。
/var/log/syslog: 查看系统日志
```

##9. 软件包管理

###a. dpkg  
dpkg是Debian系统的后台包管理器,类似RPM。也是Debian包管理系统的中流砥柱,负责安全卸载软件包,配置,以及维护已安装的软件包。由于ubuntu和Debian乃一脉相承，所以很多命令是不分彼此的。

```{bash}
dpkg -i package.deb 安装包
dpkg -r package 删除包
dpkg -P package 删除包（包括配置文件）
dpkg -L package 列出与该包关联的文件
dpkg -l package 显示该包的版本
dpkg –unpack package.deb 解开 deb 包的内容
dpkg -S keyword 搜索所属的包内容
dpkg -l 列出当前已安装的包
dpkg -c package.deb 列出 deb 包的内容
dpkg –configure package 配置包
```

###b. apt-get
apt-get是一条linux命令，适用于deb包管理式的操作系统，主要用于自动从互联网的软件仓库中搜索、安装、升级、卸载软件或操作系统。是debian，ubuntu发行版的包管理工具，与红帽中的yum工具非常类似。

软件源：

```{bash}
/etc/apt/sources.list

deb http://mirrors.163.com/ubuntu/ precise main universe restricted multiverse
deb-src http://mirrors.163.com/ubuntu/ precise main universe restricted multiverse
deb http://mirrors.163.com/ubuntu/ precise-security universe main multiverse restricted
deb-src http://mirrors.163.com/ubuntu/ precise-security universe main multiverse restricted
deb http://mirrors.163.com/ubuntu/ precise-updates universe main multiverse restricted
deb http://mirrors.163.com/ubuntu/ precise-proposed universe main multiverse restricted
deb-src http://mirrors.163.com/ubuntu/ precise-proposed universe main multiverse restricted
deb http://mirrors.163.com/ubuntu/ precise-backports universe main multiverse restricted
deb-src http://mirrors.163.com/ubuntu/ precise-backports universe main multiverse restricted
deb-src http://mirrors.163.com/ubuntu/ precise-updates universe main multiverse restricted
deb http://mirror.bjtu.edu.cn/cran/bin/linux/ubuntu precise/
```

命令:

```{bash}
apt-cache search :  (package 搜索包)
apt-cache show :  (package 获取包的相关信息，如说明、大小、版本等)
sudo apt-get install : (package 安装包)
sudo apt-get install : (package - - reinstall 重新安装包)
sudo apt-get -f install : (强制安装?#"-f = --fix-missing"当是修复安装吧...)
sudo apt-get remove : (package 删除包)
sudo apt-get remove --purge : (package 删除包，包括删除配置文件等)
sudo apt-get autoremove --purge : (package 删除包及其依赖的软件包+配置文件等（只对6.10有效，强烈推荐）)
sudo apt-get update : 更新源
sudo apt-get upgrade : 更新已安装的包
sudo apt-get dist-upgrade :升级系统
sudo apt-get dselect-upgrade :使用 dselect 升级
apt-cache depends : (package 了解使用依赖)
apt-cache rdepends : (package 了解某个具体的依赖?#当是查看该包被哪些包依赖吧...)
sudo apt-get build-dep : (package 安装相关的编译环境)
apt-get source : (package 下载该包的源代码)
sudo apt-get clean && sudo apt-get autoclean :清理下载文件的存档 && 只清理过时的包
sudo apt-get check :检查是否有损坏的依赖
```

##10. 常用软件

###a. 远程管理

```{bash}
telnet: 远程登录Linux主机
ssh: 远程安全登录Linux主机
scp: 基于ssh登陆进行安装的远程文件传输
ftp: 通过ftp协议远程文件传输
wget: 通过http协议远程文件下载
```

###b. 程序编译

```{bash}
./configure：是用来检测你的安装平台的目标特征的。configure是一个脚本，用来确定所处系统的细节，比如使用何种编译器、何种库，以及编译器和库的保存位置，并把Makefile.in的相应部分进行替换，形成Makefile。
make: 是用来编译的，它从Makefile中读取指令，然后编译。
make install: 是用来安装的，它也从Makefile中读取指令，安装到指定的位置。
make clean: 删除临时文件
```

###c. 产生密钥

```{bash}
ssh-keygen:产生SSH认证RSA密钥
ssh 是一个专为远程登录会话和其他网络服务提供安全性的协议。默认状态下ssh链接是需要密码认证的，可以通过添加系统认证（即公钥-私钥）的修改，修改后系统间切换可以避免密码输入和ssh认证.

~ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/conan/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/conan/.ssh/id_rsa.
Your public key has been saved in /home/conan/.ssh/id_rsa.pub.
The key fingerprint is:
40:9b:fe:ad:76:29:79:20:b5:7c:74:e4:c9:46:73:f1 conan@conan
The key's randomart image is:
+--[ RSA 2048]----+
|      .       .. |
|     . o    + .. |
|      +    = +  E|
|     . .. . *    |
|      .oSo o     |
|      ..+..      |
|       ..+..     |
|        +.+      |
|       ..+       |
+-----------------+

ls ~/.ssh
id_rsa  id_rsa.pub  known_hosts authorized_keys

id_rsa：私钥
id_rsa.pub: 公钥
known_hosts：登陆认证过的主机信息
authorized_keys：公钥授权文件
```

###d. 时间管理
参考文章：http://blog.fens.me/linux-ntp/

```{bash}
ntp：服务器是用来使计算机时间同步化的一种协议，它可以使计算机对其服务器或时钟源做同步化，它可以提供高精准度的时间校正，在LAN上与标准间差小于1毫秒，WAN上几十毫秒，且通过加密确认的方式来防止恶毒的协议攻击。

#服务器配置：
sudo apt-get install ntp

ps -aux|grep ntp
ntp      23717  0.0  0.0  39752  2184 ?        Ss   19:40   0:00 /usr/sbin/ntpd -p /var/run/ntpd.pid -g -u 126:135

/etc/init.d/ntp status
* NTP server is running

#客户端同步：
ntpdate 0.cn.pool.ntp.org  
22 Jul 16:45:05 ntpdate[6790]: step time server 218.75.4.130 offset 7618978.781348 sec
```

###e. 定时任务

```{bash}
crontab:设置任务调度(el)
分钟m(0-59), 小时h(1-23), 日期dom(1-31), 月份mon(1-12), 星期down(0-6,0表示周日) 

crobtab -e
# m h    dom mon dow   command
# 每周一早点5:00给目录打包
# 0 5     *   *   1    tar -zcf /var/backups/home.tgz /home/

# 每天同步2次时间。(8:00, 16:00)
# 0 8,16  *   *   *    /usr/sbin/ntpdate 192.168.1.79

```

###f. 网络文件系统

```{bash}
nfs：网络文件系统（NFS，Network File System）是一种将远程主机上的分区（目录）经网络挂载到本地系统的一种机制，通过对网络文件系统的支持，用户可以在本地系统上像操作本地分区一样来对远程主机的共享分区（目录）进行操作。

#服务器端安装
sudo apt-get install nfs-kernel-server

#配置访问权限
vi /etc/exports
/home/conan/.ssh *(rw,no_root_squash,sync)

#重启
sudo /etc/init.d/nfs-kernel-server restart

#查看配置列表
showmount -e
Export list for conan:
/home/conan/.ssh  *

#创建目录
sudo mkdir /mnt/ssh

#挂载目录
sudo mount 192.168.1.201:/home/conan/.ssh /mnt/ssh/

#查看目录
ls /mnt/ssh/
id_rsa  id_rsa.pub  known_hosts
```

###g. 域名服务器
参考文章：http://blog.fens.me/vps-ip-dns/

```{bash}
bind9：一种开源的DNS（Domain Name System）协议的实现，包含对域名的查询和响应所需的所有软件。它是互联网上最广泛使用的一种DNS服务器。

#安装
sudo apt-get install bind9
cd /etc/bind

#配置
vi named.conf.options
forwarders {
      8.8.8.8;
      8.8.4.4;
};

# 设置wtmart.com域
~ vi named.conf.local
zone "wtmart.com" {
     type master;
     file "/etc/bind/db.wtmart.com";
};

# 配置域名指向IP
~ vi db.wtmart.com
$TTL    604800
@       IN      SOA     wtmart.com. root.wtmart.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      wtmart.com.
@       IN      A       173.255.193.179
@       IN      AAAA    ::1

ip      IN      A       173.255.193.179

lin    IN    A    116.24.133.86

#重启服务器
sudo /etc/init.d/bind9 restart
```

###h. 数据库服务器

```{bash}
mysql: 是一个关系型数据库管理系统，由瑞典MySQL AB公司开发，目前属于Oracle公司。MySQL是一种关联数据库管理系统，关联数据库将数据保存在不同的表中，而不是将所有数据放在一个大仓库内，这样就增加了速度并提高了灵活性。MySQL的SQL语言是用于访问数据库的最常用标准化语言。MySQL软件采用了双授权政策（本词条“授权政策”），它分为社区版和商业版，由于其体积小、速度快、总体拥有成本低，尤其是开放源码这一特点，一般中小型网站的开发都选择MySQL作为网站数据库。

#安装
sudo apt-get install mysql-server

#配置允许其他计算访问
sudo sed -i 's/127.0.0.1/0.0.0.0/g' /etc/mysql/my.cnf

#设置字符集UTF-8
vi /etc/mysql/my.cnf

[client]
port            = 3306
socket          = /var/run/mysqld/mysqld.sock
default-character-set=utf8

[mysqld]
#
# * Basic Settings
#
default-storage-engine=INNODB
character-set-server=utf8
collation-server=utf8_general_ci

#查看字符集
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

#重启
sudo service mysql restart
```

####转载请注明出处：
http://blog.fens.me/rhadoop-linux-basic/







































































