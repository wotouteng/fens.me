时间同步服务器NTP
==============

[ubuntu实用工具系列文章](http://blog.fens.me/series-ubuntu/)，将介绍基于Linux ubuntu的各种工具软件的配置和使用。有些工具大家早已耳熟能详，有些工具经常用到但确依然陌生。我将记录我在使用操作系统时，安装及配置工具上面的一些方法，把使用心得记录下来也便于自己的以后查找和回忆。

#### 关于作者
+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/linux-ntp/

![时间同步服务器NTP](http://blog.fens.me/wp-content/uploads/2013/07/ntp-title.png)

#### 前言

服务器的时间同步操作，是每台服务器应该配置的核心功能之一。不管是单机的定时任务，还是大规模的集群服务，都需要有一个统计的时间基准，才能保证程序的正常运行。

#### 目录

1. 配置同步服务器ntp
2. 客户机进行时间同步
3. 系统时间 和 bios硬件时间hwclock

## 1. 配置同步服务器ntp

host： 192.168.1.79

#### 系统环境

Linux Ubuntu 12.10 64位 server

```{bash}
~ uname -a
Linux delta 3.5.0-17-generic #28-Ubuntu SMP Tue Oct 9 19:31:23 UTC 2012 x86_64 x86_64 x86_64 GNU/Linux

~ cat /etc/issue
Ubuntu 12.10 \n \l
```

### 安装并启动ntp服务器

```{bash}
~ sudo apt-get install ntp

~ ps -aux|grep ntp
ntp      23717  0.0  0.0  39752  2184 ?        Ss   19:40   0:00 /usr/sbin/ntpd -p /var/run/ntpd.pid -g -u 126:135

~ /etc/init.d/ntp status
 * NTP server is running
```

## 2. 客户机进行时间同步

client： 192.168.1.41

### 使用ntpdate命令

```{bash}
~ ntpdate
 1 Jul 19:58:48 ntpdate[2056]: no servers can be used, exiting

~ ping 192.168.1.79
PING 192.168.1.79 (192.168.1.79) 56(84) bytes of data.
64 bytes from 192.168.1.79: icmp_req=1 ttl=64 time=0.274 ms
64 bytes from 192.168.1.79: icmp_req=2 ttl=64 time=0.285 ms
```

### 时间同步操作

```{bash}
~ whereis ntpdate
ntpdate: /usr/sbin/ntpdate /usr/share/man/man8/ntpdate.8.gz

#当前时间
~  date
Mon Jul  1 20:05:33 CST 2013

#重置时间
~ sudo date -s 19:00:00
Mon Jul  1 19:00:00 CST 2013
~ date
Mon Jul  1 19:00:03 CST 2013

#同步时间
~ sudo /usr/sbin/ntpdate 192.168.1.79
 1 Jul 20:07:02 ntpdate[2084]: step time server 192.168.1.79 offset 3987.719712 sec

~ date
Mon Jul  1 20:07:10 CST 2013
```

### 同步时间成功，设置定时任务，每天同步2次时间。(8:00, 16:00)

```{bash}
#编辑cron任务
~ sudo crontab -e
0 8,16 * * * /usr/sbin/ntpdate 192.168.1.79

#查看cron设置
~ sudo crontab -l
0 8,16 * * * /usr/sbin/ntpdate 192.168.1.79

#重启cron任务
~ sudo /etc/init.d/cron restart
cron stop/waiting
cron start/running, process 2190
```

完成时间同步的设置。

## 3. 系统时间和bios硬件时间

下面我们做多一点时间的测试：

host机, 先后打出下面两条命令：时间是正常的

```{bash}
#系统时间
~ date
Mon Jul  1 20:18:54 CST 2013

#硬件时间
~  sudo hwclock --show
Mon 01 Jul 2013 08:19:00 PM CST  -0.172315 seconds
```

client机，先后打出下面两条命令，发现两个命令有时间差。

```{bash}
#系统时间
~ date
Mon Jul  1 20:22:31 CST 2013

#硬件时间
~ sudo hwclock --show
Mon 01 Jul 2013 08:24:38 PM CST  -0.927363 seconds
```

也就是说，刚才client在同步host时，系统时间已经改变了，而硬件的bios时间没有改变，所以出现了软件和硬件的时间不同步的情况。如果client被重启时，系统就会使用硬件时间。

### 下面同步系统时间，写入到硬件bios

```{bash}
#写入到硬件bios
~ sudo hwclock --systohc

~ date
Mon Jul  1 20:27:19 CST 2013
~ sudo hwclock --show
Mon 01 Jul 2013 08:27:25 PM CST  -0.720095 seconds
```

好了，这回把时间就设置好了！！

#### 转载请注明出处：http://blog.fens.me/linux-ntp/


