让Hadoop跑在云端系列文章 之 创建Hadoop母体虚拟机
=================

[让Hadoop跑在云端系列文章](http://blog.fens.me/series-hadoop-cloud/)，介绍了如何整合虚拟化和Hadoop，让Hadoop集群跑在VPS虚拟主机上，通过云向用户提供存储和计算的服务。

现在硬件越来越便宜，一台非品牌服务器，2颗24核CPU，配48G内存，2T的硬盘，已经降到2万块人民币以下了。这种配置如果简单地放几个web应用，显然是奢侈的浪费。就算是用来实现单节点的hadoop，对计算资源浪费也是非常高的。对于这么高性能的计算机，如何有效利用计算资源，就成为成本控制的一项重要议题了。

通过虚拟化技术，我们可以将一台服务器，拆分成12台VPS，每台2核CPU，4G内存，40G硬盘，并且支持资源重新分配。多么伟大的技术啊！现在我们有了12个节点的hadoop集群， 让Hadoop跑在云端，让世界加速。

#### 关于作者：

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：

http://blog.fens.me/hadoop-base-kvm/

![创建Hadoop母体虚拟机](http://blog.fens.me/wp-content/uploads/2013/07/clone-base.png)

#### 前言

通过虚拟化技术，我们可轻松的增加或删除一台虚拟机。结合hadoop技术，我们需要先创建一台配置好hadoop环境虚拟机，作为克隆的母体。

非虚拟化安装Hadoop集群，请参考：[RHadoop实践系列之一:Hadoop环境搭建](http://blog.fens.me/rhadoop-hadoop/)

#### 目录

1. HOST系统环境
2. 拟机资源分配策略
3. 创建Hadoop母体虚拟机
4. 配置Hadoop环境

## 1. HOST系统环境

关于虚拟化的基础知识，请参考：自己搭建VPS系列文章

### KVM技术，HOST系统环境：
Linux Ubuntu 12.10 64bit server  
24核CPU，48G内存，硬盘：300G(SAS)+1T(SATA)+1T(SATA)

```{bash}

~ uname -a
Linux delta 3.5.0-17-generic #28-Ubuntu SMP Tue Oct 9 19:31:23 UTC 2012 x86_64 x86_64 x86_64 GNU/Linux

~ cat /etc/issue
Ubuntu 12.10 \n \l

~ top
top - 10:54:53 up 2 days, 11:39,  5 users,  load average: 4.50, 4.76, 3.87
Tasks: 345 total,   2 running, 343 sleeping,   0 stopped,   0 zombie
%Cpu0  :  8.6 us,  1.0 sy,  0.0 ni, 88.7 id,  0.0 wa,  0.0 hi,  1.7 si,  0.0 st
%Cpu1  : 14.2 us,  1.7 sy,  0.0 ni, 83.7 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu2  :  7.3 us,  0.7 sy,  0.0 ni, 91.7 id,  0.0 wa,  0.0 hi,  0.3 si,  0.0 st
%Cpu3  : 10.9 us,  2.7 sy,  0.0 ni, 86.0 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu4  :  4.0 us,  0.3 sy,  0.0 ni, 95.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu5  :  9.8 us,  2.7 sy,  0.0 ni, 87.1 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu6  :  7.3 us,  1.3 sy,  0.0 ni, 91.4 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu7  : 15.6 us,  1.7 sy,  0.0 ni, 82.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu8  :  6.3 us,  0.7 sy,  0.0 ni, 93.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu9  : 14.9 us,  1.7 sy,  0.0 ni, 83.1 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu10 :  6.9 us,  1.0 sy,  0.0 ni, 92.1 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu11 : 18.6 us,  2.1 sy,  0.0 ni, 79.0 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu12 :  1.7 us,  0.0 sy,  0.0 ni, 98.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu13 : 18.5 us,  1.0 sy,  0.0 ni, 80.5 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu14 :  2.3 us,  0.3 sy,  0.0 ni, 97.4 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu15 :  1.0 us,  0.7 sy,  0.0 ni, 98.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu16 :  1.3 us,  0.0 sy,  0.0 ni, 98.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu17 :  3.7 us,  0.3 sy,  0.0 ni, 96.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu18 :  1.7 us,  0.7 sy,  0.0 ni, 97.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu19 :  1.3 us,  1.3 sy,  0.0 ni, 97.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu20 :  3.3 us,  0.3 sy,  0.0 ni, 96.4 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu21 : 36.3 us,  1.3 sy,  0.0 ni, 62.0 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu22 :  4.9 us,  1.3 sy,  0.0 ni, 93.8 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu23 :  4.0 us,  0.7 sy,  0.0 ni, 95.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem:  49449836 total, 49219824 used,   230012 free,  8737312 buffers
KiB Swap: 48827388 total,    57944 used, 48769444 free, 21761620 cached

~ sudo fdisk -l

Disk /dev/sda: 299.4 GB, 299439751168 bytes
255 heads, 63 sectors/track, 36404 cylinders, total 584843264 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x000efd7c

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1            2048    97656831    48827392   82  Linux swap / Solaris
/dev/sda2        97656832   136718335    19530752   83  Linux
/dev/sda3       136718336   214843335    39062500   83  Linux
/dev/sda4   *   214843392   215037951       97280   83  Linux

Disk /dev/sdb: 1999.3 GB, 1999307276288 bytes
255 heads, 63 sectors/track, 243068 cylinders, total 3904897024 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0xf919a976

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048  1952448511   976223232    7  HPFS/NTFS/exFAT
/dev/sdb2      1952448512  3904897023   976224256    5  Extended
/dev/sdb5      1952450560  2267023360   157286400+  83  Linux
/dev/sdb6      2267025409  2581596160   157285376   83  Linux
/dev/sdb7      2581598209  2896168960   157285376   83  Linux
/dev/sdb8      2896171009  3210741760   157285376   83  Linux
/dev/sdb9      3210743809  3525314560   157285376   83  Linux
/dev/sdb10     3525316609  3904897023   189790207+  83  Linux
```

## 2. 虚拟机资源分配策略

### hadoop节点
我们准备做5个hadoop节点：c1,c2,c3,c4,c5  
c1为namenode，c2,c3,c4,c5作为datanode

### 硬盘资源分配
其中/dev/sdb2的硬盘为扩充分区，支持6个逻辑分区：sdb5，sdb6，sdb7，sdb8，sdb9，sdb10  
每台虚拟机2个CPU，4G内存，硬盘40G(基础空间)+150G(分区硬盘)
+ c1挂载分区硬盘sdb5
+ c2挂载分区硬盘sdb6
+ c3挂载分区硬盘sdb7
+ c4挂载分区硬盘sdb8
+ c5挂载分区硬盘sdb9  
给虚拟机挂载硬盘，请参考：[给KVM虚拟机增加硬盘](http://blog.fens.me/vps-kvm-disk/)

### IP地址分配：
+ c1：192.168.1.30
+ c2：192.168.1.31
+ c3：192.168.1.32
+ c4：192.168.1.33
+ c5：192.168.1.34

### DNS映射：bind9服务
+ c1 IN A 192.168.1.30
+ c2 IN A 192.168.1.31
+ c3 IN A 192.168.1.32
+ c4 IN A 192.168.1.33
+ c5 IN A 192.168.1.34

### hostname
绑定在GUEST中配置/etc/hostname  
c1,c2,c3,c4,c5

## 3. 创建Hadoop母体虚拟机

如何创建虚拟机，请参考：自己搭建VPS系列 之 在Ubuntu上安装KVM

### 创建c1

```{bash}

~ sudo virt-install --connect=qemu:///system \
--name c1 \
--ram 4096 \
--vcpus=2 \
--os-type=linux \
--os-variant=ubuntuprecise \
--accelerate \
--hvm \
--disk path=/disk/sdb1/c1.img,size=100,bus=virtio \
--location /home/cos/os/u1210 \
--extra-args='console=tty0 console=ttyS0' \
--network bridge=br0,model=virtio \
--graphics none
```

### 启动c1

```{bash}
~ sudo virsh
Welcome to virsh, the virtualization interactive terminal.

Type:  'help' for help with commands
       'quit' to quit

virsh # start c1
Domain c1 started

#访问c1
virsh # console c1
Connected to domain c1
Escape character is ^]

#登陆c1
Last login: Tue Jul  9 23:41:20 CST 2013 from 192.168.1.79 on pts/0
Welcome to Ubuntu 12.10 (GNU/Linux 3.5.0-32-generic x86_64)

 * Documentation:  https://help.ubuntu.com/
New release '13.04' available.
Run 'do-release-upgrade' to upgrade to it.

cos@localhost:~$
```

### 修改IP地址:设置静态IP

```{bash}
~ sudo vi /etc/network/interfaces

auto lo
iface lo inet loopback

# The primary network interface
auto eth0
#iface eth0 inet dhcp
iface eth0 inet static
address 192.168.1.30
netmask 255.255.255.0
gateway 192.168.1.1
```

### 修改hostname，及本地hosts映射

```{bash}
~ sudo vi /etc/hostname
c1

~ sudo vi /etc/hosts
127.0.1.1       c1
```

注：退出virsh console环境：right-ctrl和-] ，右边ctrl + 减号(-) + 右括号(])

### 重启c1

```{bash}
virsh # list
 Id    Name                           State
----------------------------------------------------
 5     server3                        running
 6     server4                        running
 7     d2                             running
 8     r1                             running
 9     server2                        running
 18    server5                        running
 40    c1                             running

virsh # destroy c1
Domain c1 destroyed

virsh # start c1
Domain c1 started
```

### 命令行登陆访问

```{bash}
~ ssh cos@c1.wtmart.com

#查看本机IP
~ ifconfig
eth0      Link encap:Ethernet  HWaddr 52:54:00:93:02:fa
          inet addr:192.168.1.30  Bcast:192.168.1.255  Mask:255.255.255.0
          inet6 addr: fe80::5054:ff:fe93:2fa/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:66 errors:0 dropped:0 overruns:0 frame:0
          TX packets:32 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:10321 (10.3 KB)  TX bytes:4479 (4.4 KB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:16436  Metric:1
          RX packets:34 errors:0 dropped:0 overruns:0 frame:0
          TX packets:34 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:2560 (2.5 KB)  TX bytes:2560 (2.5 KB)
```

## 4. 配置Hadoop环境

非虚拟化安装Hadoop，请参考：[RHadoop实践系列之一:Hadoop环境搭建](http://blog.fens.me/rhadoop-hadoop/)

### 当前用户及用户路径

```{bash}

#192.168.1.79计算机IP是host
~ who
cos      pts/0        2013-07-10 11:27 (192.168.1.79)

~ pwd
/home/cos
```

### 工作目录

download目录：各种软件包
toolkit目录：软件安装目录
hadoop目录：hadoop的数据存储目录(**挂载分区硬盘**/dev/sdb5)

```{bash}
~ ls -l
drwxrwxr-x 2 cos cos 4096 Jul  9 21:42 download
drwxrwxr-x 2 cos cos 4096 Jul  9 23:50 hadoop
drwxrwxr-x 7 cos cos 4096 Jul  9 23:31 toolkit
```

### 自行下载各种程序包

```{bash}
~ ls -l download/
-rwxr-xr-x 1 cos cos   5425171 Jul  8 21:17 apache-ant-1.8.4-bin.tar.gz
-rwxr-xr-x 1 cos cos   4873043 Jul  8 21:01 apache-maven-3.0.4-bin.tar.gz
-rwxr-xr-x 1 cos cos  48350337 Jul  8 21:15 apache-nutch-1.6-bin.tar.gz
-rwxr-xr-x 1 cos cos   7645670 Jul  8 21:02 apache-tomcat-7.0.27.tar.gz
-rwxr-xr-x 1 cos cos  62428860 Jul  8 21:13 hadoop-1.0.3.tar.gz
-rwxr-xr-x 1 cos cos  48433847 Jul  8 21:17 hbase-0.94.2.tar.gz
-rwxr-xr-x 1 cos cos  30195232 Jul  8 20:59 hive-0.9.0.tar.gz
-rwxr-xr-x 1 cos cos  85411605 Jul  8 21:05 jdk-6u29-linux-x64.bin
-rwxr-xr-x 1 cos cos   6348405 Jul  8 21:01 phpmyadmin353.zip
-rwxr-xr-x 1 cos cos  48307928 Jul  8 21:01 pig-0.10.0.tar.gz
-rwxr-xr-x 1 cos cos 121058888 Jul  8 21:10 solr-4.2.0.zip
-rwxr-xr-x 1 cos cos   4782922 Jul  8 21:15 sqoop-1.4.2.bin__hadoop-1.0.0.tar.gz
-rwxr-xr-x 1 cos cos   2336261 Jul  8 21:17 thrift-0.8.0.tar.gz
-rwxr-xr-x 1 cos cos   2794605 Jul  8 21:02 thrift-0.9.0.tar.gz
-rwxr-xr-x 1 cos cos  16347805 Jul  8 21:18 zookeeper-3.4.4.tar.gz
```

### 安装Java,ant,maven到toolkit目录

```{bash}
ls -l toolkit/
drwxr-xr-x  6 cos cos 4096 May 22  2012 ant184
drwxr-xr-x 10 cos cos 4096 Jul  8 21:24 jdk16
drwxrwxr-x  6 cos cos 4096 Jul  9 18:40 maven
```

### 配置环境变更

```{bash}
~ sudo vi /etc/environment

PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/home/cos/toolkit/ant184/bin:/home/cos/toolkit/jdk16/bin:/home/cos/toolkit/maven3/bin"

JAVA_HOME=/home/cos/toolkit/jdk16
ANT_HOME=/home/cos/toolkit/ant184
MAVEN_HOME=/home/cos/toolkit/maven3

CLASSPATH=/home/cos/toolkit/jdk16/lib/dt.jar:/home/cos/toolkit/jdk16/lib/tools.jar

#加载到当前环境中
~ . /etc/environment
```

### 安装hadoop到toolkit目录

```{bash}
~ tar zxvf download/hadoop-1.0.3.tar.gz
~ mv hadoop-1.0.3 toolkit
```

### 修改配置文件

```{bash}
~ cd /home/cos/toolkit/hadoop-1.0.3/conf

~ vi core-site.xml
<configuration>
<property>
<name>fs.default.name</name>
<value>hdfs://c1.wtmart.com:9000</value>
</property>
<property>
<name>hadoop.tmp.dir</name>
<value>/home/cos/hadoop/tmp</value>
</property>
</configuration>

~ vi hdfs-site.xml
<configuration>
<property>
<name>dfs.data.dir</name>
<value>/home/cos/hadoop/data</value>
</property>
<property>
<name>dfs.replication</name>
<value>1</value>
</property>
<property>
<name>dfs.permissions</name>
<value>false</value>
</property>
<property>
<name>dfs.support.append</name>
<value>true</value>
</property>
</configuration>

~ vi mapred-site.xml
<configuration>
<property>
<name>mapred.job.tracker</name>
<value>hdfs://c1.wtmart.com:9001</value>
</property>
</configuration>

~ vi master
c1.wtmart.com

~ vi slaves
c1.wtmart.com
```

### 配置hadoop环境变量

```{bash}
~ sudo vi /etc/environment

PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/home/cos/toolkit/ant184/bin:/home/cos/toolkit/jdk16/bin:/home/cos/toolkit/maven3/bin:/home/cos/toolkit/hadoop-1.0.3/bin"

JAVA_HOME=/home/cos/toolkit/jdk16
ANT_HOME=/home/cos/toolkit/ant184
MAVEN_HOME=/home/cos/toolkit/maven3

HADOOP_HOME=/home/cos/toolkit/hadoop-1.0.3

CLASSPATH=/home/cos/toolkit/jdk16/lib/dt.jar:/home/cos/toolkit/jdk16/lib/tools.jar
```

### 接下来，我们挂载分区硬盘/dev/sdb5到/home/cos/hadoop
细节说明，请参考：[给KVM虚拟机增加硬盘](http://blog.fens.me/vps-kvm-disk/)

```{bash}
virsh # edit c1
Domain c1 XML configuration edited.

<disk type='block' device='disk'>
<driver name='qemu' type='raw' cache='none'/>
<source dev='/dev/sdb5'/>
<target dev='vdb' bus='virtio'/>
</disk>

virsh # destroy c1
Domain c1 destroyed

virsh # start c1
Domain c1 started
```

### 回到虚拟机查看新的硬盘

```{bash}
~ sudo fdisk -l

Disk /dev/vdb: 161.1 GB, 161061274112 bytes
4 heads, 4 sectors/track, 19660800 cylinders, total 314572801 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x3b49c6a0

   Device Boot      Start         End      Blocks   Id  System
/dev/vdb1            2048   314572800   157285376+  83  Linux

#挂载硬盘
~ sudo mount /dev/vdb1 /home/cos/hadoop
~ df -h
Filesystem              Size  Used Avail Use% Mounted on
/dev/mapper/u1210-root   36G  2.4G   32G   7% /
udev                    2.0G  4.0K  2.0G   1% /dev
tmpfs                   791M  228K  791M   1% /run
none                    5.0M     0  5.0M   0% /run/lock
none                    2.0G     0  2.0G   0% /run/shm
none                    100M     0  100M   0% /run/user
/dev/vda1               228M   29M  188M  14% /boot
/dev/vdb1               148G  6.7G  134G   5% /home/cos/hadoop

#设置开机自己挂载
~ sudo vi /etc/fstab
/dev/vdb1                         /home/cos/hadoop      ext4    defaults         0       0

```

### ssh无密码登陆

```{bash}
~ ssh-keygen -t rsa
~ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

### 对hadoop出始化

```{bash}
~ mkdir hadoop/tmp
~ mdkir hadoop/data

~ hadoop namenode -format
Warning: $HADOOP_HOME is deprecated.

13/07/10 12:03:41 INFO namenode.NameNode: STARTUP_MSG:
/************************************************************
STARTUP_MSG: Starting NameNode
STARTUP_MSG:   host = c1/127.0.1.1
STARTUP_MSG:   args = [-format]
STARTUP_MSG:   version = 1.0.3
STARTUP_MSG:   build = https://svn.apache.org/repos/asf/hadoop/common/branches/branch-1.0 -r 1335192; compiled by 'hortonfo' on Tue May  8 20:31:25 UTC 2012
************************************************************/
Re-format filesystem in /home/cos/hadoop/tmp/dfs/name ? (Y or N) y
Format aborted in /home/cos/hadoop/tmp/dfs/name
13/07/10 12:03:50 INFO namenode.NameNode: SHUTDOWN_MSG:
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at c1/127.0.1.1
************************************************************/
```

### 测试hadoop是否正常运行

```{bash}
~ start-all.sh
Warning: $HADOOP_HOME is deprecated.

starting namenode, logging to /home/cos/toolkit/hadoop-1.0.3/libexec/../logs/hadoop-cos-namenode-c1.out
c1.wtmart.com: Warning: $HADOOP_HOME is deprecated.
c1.wtmart.com:
c1.wtmart.com: starting datanode, logging to /home/cos/toolkit/hadoop-1.0.3/libexec/../logs/hadoop-cos-datanode-c1.out
c1.wtmart.com: Warning: $HADOOP_HOME is deprecated.
c1.wtmart.com:
c1.wtmart.com: starting secondarynamenode, logging to /home/cos/toolkit/hadoop-1.0.3/libexec/../logs/hadoop-cos-secondarynamenode-c1.out
starting jobtracker, logging to /home/cos/toolkit/hadoop-1.0.3/libexec/../logs/hadoop-cos-jobtracker-c1.out
c1.wtmart.com: Warning: $HADOOP_HOME is deprecated.
c1.wtmart.com:
c1.wtmart.com: starting tasktracker, logging to /home/cos/toolkit/hadoop-1.0.3/libexec/../logs/hadoop-cos-tasktracker-c1.out

~ jps
1290 DataNode
1729 Jps
1663 TaskTracker
1419 SecondaryNameNode
1535 JobTracker
1167 NameNode

#上传一个本地文件到hdfs
~ vi test.txt
hello world!!

~ hadoop fs -copyFromLocal test.txt /
~ hadoop fs -cat /test.txt
hello world!!
```

Hadoop母体虚拟机已创建完毕，下面我们就可以以克隆的方式创建Hadoop虚拟节点了。

####转载请注明出处：http://blog.fens.me/hadoop-base-kvm/































