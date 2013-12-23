upstart把应用封装成系统服务
=================

[ubuntu实用工具系列文章](http://blog.fens.me/series-ubuntu/)，将介绍基于Linux ubuntu的各种工具软件的配置和使用。有些工具大家早已耳熟能详，有些工具经常用到但确依然陌生。我将记录我在使用操作系统时，安装及配置工具上面的一些方法，把使用心得记录下来也便于自己的以后查找和回忆。

#### 关于作者
+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/linux-upstart/

![upstart把应用封装成系统服务](http://blog.fens.me/wp-content/uploads/2013/06/upstart-basic1.png)

#### 前言

对于使用linux的同学，敲大段的命令都已经是家长便饭了。但有些时候用命令也不是那么方便，比如启动一个后台程序, sh ./app & 执行启动命令很简单，如果想停止这个应用或者重启这个应用，就不是那么方便了。

如果能把应用封装成系统服务，那么就可以使用的 启动，重起，停止，状态检查等的标准方法了。应用会像一个守护程序一样，被操作系统所管理。

#### 文章目录

1. 初识upstart
2. upstart任务文件的语法
3. upstart命令
4. upstart封装mytest
5. 例子：upstart封装Nginx
6. 例子：upstart封装MySQL
7. 例子：upstart封装ssh

## 1. 初识upstart

说来惭愧，用了很多年的linux，也没有想法去怎么能优化一下应用启动。最近部署Nodejs应用时才发现，原来ubuntu有这么优雅的应用管理方式。

upstart可以用来代替/etc/init.d/的执行脚本，额外提供了一些特性，像速度，状态检查，简单定义任务等。

**upstart两个核心点：事件(events)，任务(jobs)**

事件状态图

![](http://blog.fens.me/wp-content/uploads/2013/06/upstart.png)

对状态的描述

+ waiting : initial state.
+ starting : job is about to start.
+ pre-start : running pre-start section.
+ spawned : about to run script or exec section.
+ post-start : running post-start section.
+ running : interim state set after post-start section processed denoting job is running (But it may have no associated PID!)
+ pre-stop : running pre-stop section.
+ stopping : interim state set after pre-stop section processed.
+ killed : job is about to be stopped.
+ post-stop : running post-stop section.

每个任务需要有一个配置文件，存放在/etc/init/目录下面。

```{bash}
~ vi /etc/init/mytest.conf
```

完成任务配置文件后，可以直接用initctl, start, stop 命令对任务进行启动，停止，查看状态 等的操作。

```{bash}
start mytest.conf
```

**注：下面所有实例都以root权限进行操作**

本文主要以实践为主。原理及更多细节介绍，请查看Upstart Cookbook:

http://upstart.ubuntu.com/cookbook/

## 2. upstart任务文件的语法

详细介绍： http://upstart.ubuntu.com/wiki/Stanzas
*注：Stanzas的文档很多内容已经过期，建议参考cookbook或者man的使用帮助。*

### 任务文件支持的语法关键字

Process Definition:

```{bash}
exec, script, pre-start, post-start, pre-stop, post-stop,
```

Event Definition:

```{bash}
start on, stop on, manual
```

Job Environment:

```{bash}
env, export
```

Services, tasks and respawning:

```{bash}
normal exit, respawn, respawn limit, task
```

Instances:

```{bash}
instance
```

Documentation:

```{bash}
description, author, version, emits, usage
```

Process environment:

```{bash}
console none,console log, console output, console owner, nice, limit, chroot, chdir, oom score, setgid, setuid, umask
```

Process Control:

```{bash}
expect fork, expect daemon, expect stop, kill signal, kill timeout
```

过期关键字：

```{bash}
service, daemon, pid
```

#### 部分语法关键字介绍:

**exec : 执行命令，在script块中使用。**

语法：

```{bash}
exec /usr/bin/zip -v
```

#### script: 脚本块，包括主运行脚本

语法：

```{bash}

script
    exec /usr/bin/zip /root/upstart.zip /root/upstart.txt
end script
```

### pre-start: 脚本块，在主运行脚本之前执行的脚本

语法：

```{bash}
pre-start script
    exec rm /root/upstart.txt
    exec echo pre-start >> /root/upstart.txt
end script
```

### post-start: 脚本块，在主运行脚本之后，running状态之前

语法：

```{bash}
post-start script
    exec echo post-start >> /root/upstart.txt
end script
```

### pre-stop: 脚本块，在执行stop之前

语法：

```{bash}
pre-stop script
    exec echo pre-stop >> /root/upstart.txt
end script
```

### post-stop: 脚本块，在主运行脚本被杀死之后

语法：

```{bash}
post-stop script
    exec echo post-stop >> /root/upstart.txt
end script
```

### start on: 事件，启动任务

语法：

```{bash}
start on startup
```

### stop on: 事件，停止任务

语法：

```{bash}
stop on suhtdown
```

### description: 描述，信息提示
语法：

```{bash}
description "This is a upstart testing."
```

### author: 描述，作者信息
语法：

```{bash}
author "Conan_Z <bsspirit@gmail.com>"
```

### version: 描述，版本信息
语法：

```{bash}
version "0.0.1 dev"
```

### respawn: 命令，设置服务异常停止后自动重启
语法：

```{bash}
respawn
```

### respawn limit: 命令，设置服务异常停止后重启次数及间隔时间
语法：

```{bash}
respawn limit 15 3
```

### service: 命令，0.6版本后不再使用，被respawn取代

**instance: 定义实例名字，可以通过命令给任务传参数**
  语法：

```{bash}
instance $TTY
exec /sbin/getty -8 38300 $TTY

#通过命令传参数
~ start mytest $TTY=tty1
```

### daemon: 作为守护进程的标志，0.5.0版本后被expect fork取代

**kill timeout: 命令，在到达指定时间后，停止应用**

语法：

```{bash}
kill timeout 5
```

### kill timeout: 命令，正常退出，不会被respawn重启

语法：

```{bash}
normal exit 0 TERM
```

### console: 命令，控制后输出，支持4种操作logged|output|owner|none

语法：

```{bash}
console owner
```

### env: 变量，设置任务的环境变量

语法：

```{bash}
env PIDFILE=/var/run/myprocess.pid
```

### umask: 变量，设置任务的文件权限的掩码

语法：

```{bash}
umask 0755
```

### nice: 变量，设置任务的调度优先级

语法：

```{bash}
nice -5
```

### limit: 变量，设置任务的资源限制

语法：

```{bash}
limit nproc 10 10
```

### chroot: 变量，设置任务的根目录

语法：

```{bash}
chroot /var/lib/www/jail
```

### chdir: 变量，设置任务的工作目录

语法：

```{bash}
chdir /var/tmp
```

## 3. upstart命令介绍

![](http://blog.fens.me/wp-content/uploads/2013/06/upstart-cmd.png)

查看upstart版本

```{bash}
~ initctl version
init (upstart 1.5)
```

查看mytest应用状态

```{bash}
#方法1
~ initctl list|grep mytest
#方法2
~ status mytest
```

启动mytest应用

```{bash}
#方法1：
~ initctl start mytest
#方法2
~ start mytest
```

停止mytest应用

```{bash}
#方法1：
~ initctl stop mytest
#方法2
~ stop mytest
```

## 4. upstart封装mytest

用upstart写一个简单的应用脚本。

```{bash}
~ vi /etc/init/mytest.conf

description "mytest"
author "bsspirit <http://blog.fens.me>"

env var=bar
export var

start on startup
stop on shutdown

respawn
respawn limit 2 5

console output

pre-start script
        logger "pre-start: before: var=$var"
        var=pre-start
        export var
        logger "pre-start: after: var=$var"
end script

post-start script
        logger "post-start: before: var=$var"
        var=post-start
        export var
        logger "post-start: after: var=$var"
end script

script
        logger "script: before: var=$var"
        var=main
        export var
        sleep 60000
        logger "script: after: var=$var"
end script

post-stop script
        logger "post-stop: before: var=$var"
        var=post-stop
        export var
        logger "post-stop: after: var=$var"
end script
```

### 测试mytest程序：

启动mytest任务

```{bash}
~ start mytest
mytest start/running, process 20682
```

查看日志跟踪运行状态

```{bash}
~ tail -f /var/log/syslog

Jun 21 08:11:21 li478-194 logger: pre-start: before: var=bar
Jun 21 08:11:21 li478-194 logger: pre-start: after: var=pre-start
Jun 21 08:11:21 li478-194 logger: script: before: var=bar
Jun 21 08:11:21 li478-194 logger: post-start: before: var=bar
Jun 21 08:11:21 li478-194 logger: post-start: after: var=post-start
```

查看mytest任务状态

```{bash}
~ status mytest
mytest start/running, process 20682
```

查看系统进程，因为在程序中用sleep停止，要通过sleep查询

```{bash}
~ ps -aux|grep sleep

root     20686  0.0  0.0   4304   352 ?        S    08:11   0:00 sleep 60000
```

杀掉sleep进程，mytest自动重启

```{bash}
~ kill -9 20686
~ ps -aux|grep sleep
root     20703  0.0  0.0   4304   344 ?        S    08:14   0:00 sleep 60000

~ /var/log/syslog
Jun 21 08:15:59 li478-194 logger: post-stop: before: var=bar
Jun 21 08:15:59 li478-194 logger: post-stop: after: var=post-stop
Jun 21 08:15:59 li478-194 logger: pre-start: before: var=bar
Jun 21 08:15:59 li478-194 logger: pre-start: after: var=pre-start
Jun 21 08:15:59 li478-194 logger: script: before: var=bar
Jun 21 08:15:59 li478-194 logger: post-start: before: var=bar
Jun 21 08:15:59 li478-194 logger: post-start: after: var=post-start
```

停止mytest

```{bash}
~ stop mytest
mytest stop/waiting

~ /var/log/syslog
Jun 21 08:16:49 li478-194 logger: post-stop: before: var=bar
Jun 21 08:16:49 li478-194 logger: post-stop: after: var=post-stop

~ ps -aux|grep sleep
```

通过命令传参数，启动mytest

```{bash}
~ start mytest var=conan
mytest start/running, process 20735

~ /var/log/syslog
Jun 21 08:18:51 li478-194 logger: pre-start: before: var=conan
Jun 21 08:18:51 li478-194 logger: pre-start: after: var=pre-start
Jun 21 08:18:51 li478-194 logger: script: before: var=conan
Jun 21 08:18:51 li478-194 logger: post-start: before: var=conan
Jun 21 08:18:51 li478-194 logger: post-start: after: var=post-start
```

不同参数，再次启动mytest

```{bash}
~ start mytest var=bsspirit
start: Job is already running: mytest
```

启动已经启动，禁止应用多次启动。

实验成功，我们可以很方便地利用upstart，来封装我们自己的应用成为系统服务。

下面将介绍同个软件使用upstart的例子。

## 5. upstart封装Nginx

Nginx官方配置：http://wiki.nginx.org/Upstart

增加配置文件

```{bash}

~ vi /etc/init/nginx

# nginx

description "nginx http daemon"
author "George Shammas <georgyo@gmail.com>"

start on (filesystem and net-device-up IFACE=lo)
stop on runlevel [!2345]

env DAEMON=/usr/sbin/nginx
env PID=/var/run/nginx.pid

expect fork
respawn
respawn limit 10 5
#oom never

pre-start script
        $DAEMON -t
        if [ $? -ne 0 ]
                then exit $?
        fi
end script

exec $DAEMON
```

查看nginx的系统状态

```{bash}
~ initctl list | grep nginx
```

启动nginx

```{bash}
~ initctl start nginx
```

## 6. upstart封装MySQL

查看upstart配置文件，把MySQL变为系统服务.

```{bash}

~ vi /etc/init/mysql.conf

description     "MySQL Server"
author          "Mario Limonciello <superm1@ubuntu.com>"

start on runlevel [2345]
stop on starting rc RUNLEVEL=[016]

respawn
respawn limit 2 5

env HOME=/etc/mysql
umask 007

# The default of 5 seconds is too low for mysql which needs to flush buffers
kill timeout 300

pre-start script
    #Sanity checks
    [ -r $HOME/my.cnf ]
    [ -d /var/run/mysqld ] || install -m 755 -o mysql -g root -d /var/run/mysqld
    /lib/init/apparmor-profile-load usr.sbin.mysqld
    LC_ALL=C BLOCKSIZE= df --portability /var/lib/mysql/. | tail -n 1 | awk '{ exit ($4<4096) }'
end script

exec /usr/sbin/mysqld

post-start script
   for i in `seq 1 30` ; do
        /usr/bin/mysqladmin --defaults-file="${HOME}"/debian.cnf ping && {
            exec "${HOME}"/debian-start
            # should not reach this line
            exit 2
        }
        statusnow=`status`
        if echo $statusnow | grep -q 'stop/' ; then
            exit 0
        elif echo $statusnow | grep -q 'respawn/' ; then
            exit 1
        fi
        sleep 1
    done
    exit 1
end script
```

## 7. upstart封装ssh

```{bash}
~ vi /etc/init/ssh.conf

# ssh - OpenBSD Secure Shell server
#
# The OpenSSH server provides secure shell access to the system.

description     "OpenSSH server"

start on filesystem or runlevel [2345]
stop on runlevel [!2345]

respawn
respawn limit 10 5
umask 022

# 'sshd -D' leaks stderr and confuses things in conjunction with 'console log'
console none

pre-start script
    test -x /usr/sbin/sshd || { stop; exit 0; }
    test -e /etc/ssh/sshd_not_to_be_run && { stop; exit 0; }
    test -c /dev/null || { stop; exit 0; }

    mkdir -p -m0755 /var/run/sshd
end script

# if you used to set SSHD_OPTS in /etc/default/ssh, you can change the
# 'exec' line here instead
exec /usr/sbin/sshd -D
```

我在这里介绍的内容还很初级，只要会写任务脚步就可以了。

后面文章我会介绍upstart封装nodejs，mongodb，php，java等的应用的任务脚本。

#### 转载请注明出处：http://blog.fens.me/linux-upstart/
