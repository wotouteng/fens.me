给KVM虚拟机增加硬盘
==========

[自己搭建VPS系列文章](http://blog.fens.me/series-vps/)，介绍了如何利用自己的计算机资源，通过虚拟化技术搭建VPS。

在互联网2.0时代，每个人都有自己的博客，还有很多专属于自己的互联网应用。这些应用大部分都是互联网公司提供的。对于一些有能力的开发人员(geek)来说，他们希望做一些自己的应用，可以用到最新最炫的技术，并且有自己的域名，有自己的服务器。这时就要去租一些互联网上的VPS主机。VPS主机就相当于是一台远程的计算机，可以部署自己的应用程序，然后申请一个域名，就可以正式发布在互联网上了。本站“[@晒粉丝](http://www.fens.me/)” 就使用的[Linode主机](http://www.linode.com/?r=70e684fdc20f965f06b7b6e80f6acfd25db211ec)VPS在美国达拉斯机房。

其实，VPS还可以自己搭建的。只要我们有一台高性能的服务器，一个IP地址，一个路由。可以把一台高性能的服务器，很快的变成5台，10台，20台的虚拟VPS。我们就可以在自己的VPS上面的，发布各种的应用，还可以把剩余的服务器资源租给其他的互联网使用者。 本系列文章将分为以下几个部分介绍：“虚拟化技术选型”，[“动态IP解析”](http://blog.fens.me/vps-ip-dns)，[“在Ubuntu上安装KVM并搭建虚拟环境”](http://blog.fens.me/vps-kvm/)，[“给KVM虚拟机增加硬盘”](http://blog.fens.me/vps-kvm-disk)，[“Nova安装攻略”](http://blog.fens.me/vps-nova-setup)，“VPS内网的网络架构设计”，“VPS租用云服务”。

#### 关于作者：

+ 张丹(Conan), 程序员Java,R,PHP,Javacript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/vps-kvm/

![自己搭建VPS系列文章](http://blog.fens.me/wp-content/uploads/2013/07/kvm-disk.png)

#### 前言

虚拟机作为灵活配置的服务器主机，给系统运维和管理带来了巨大的便利。CPU，内存，硬盘，网络等的可配置，给了虚拟机非常强大的优势，是物理机不能比拟的。今天讲一下如何给KVM虚拟机增加新硬盘。

#### 目录

1. host增加物理硬盘并分区
2. 通过virsh给guest增加文件硬盘
3. 通过virsh给guest增加分区硬盘

## 1. host增加物理硬盘并分区

HOST作为KVM的宿主计算机，管理所有GUEST虚拟机。我们通过给HOST增加物理硬盘，然后分给GUEST，从而实现给虚拟机硬盘扩容的效果。

如何给计算机增加物理硬盘并分区，请参考：[多硬盘分区管理fdisk](http://blog.fens.me/linux-fdisk/) 文章

### 查看HOST机的硬盘

```{bash}

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

~ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3        37G  3.4G   32G  10% /
udev             24G  4.0K   24G   1% /dev
tmpfs           9.5G  1.1M  9.5G   1% /run
none            5.0M  8.0K  5.0M   1% /run/lock
none             24G  152K   24G   1% /run/shm
none            100M   28K  100M   1% /run/user
cgroup           24G     0   24G   0% /sys/fs/cgroup
/dev/sda2        19G  3.6G   14G  21% /home
/dev/sda4        92M   34M   54M  39% /boot
/dev/sdb1       931G  100G  832G  11% /disk/sdb1
/dev/sdb6       148G  188M  140G   1% /disk/sdb6
```

### 下面将进行两个测试：
通过virsh给guest增加文件硬盘：通过文件硬盘的镜像/disk/sdb6/c1d6.img
  通过virsh给guest增加分区硬盘：直接使用分区硬盘/dev/sdb5

## 2. 通过virsh给guest增加文件硬盘

### 创建文件硬盘的镜像

```{bash}

~ cd /disk/sdb6/
~ sudo qemu-img create -f raw /disk/sdb6/c1d6.img 10G
Formatting '/disk/sdb6/c1d6.img', fmt=raw size=10737418240

~ ls -l
-rw-r--r-- 1 root root 10737418240 Jul  8 16:37 c1d6.img
drwx------ 2 root root       16384 Jul  8 09:03 lost+found/
```

### 通过virsh管理工具加载硬盘

```{bash}

~ sudo virsh
Welcome to virsh, the virtualization interactive terminal.
Type:  'help' for help with commands
       'quit' to quit

#查看系统内的虚拟机
virsh # list
Id Name State
----------------------------------------------------
5 server3 running
6 server4 running
7 d2 running
8 r1 running
9 server2 running
12 c1 running

#在这里我们要对c1进行硬盘扩容
virsh # edit c1

#找到硬盘配置(原来的系统硬盘)
<disk type='file' device='disk'>
<driver name='qemu' type='raw'/>
<source file='/disk/sdb1/c1.img'/>
<target dev='vda' bus='virtio'/>
<address type='pci' domain='0x0000' bus='0x00' slot='0x04' function='0x0'/>
</disk>

#增加文件硬盘,vdb
<disk type='file' device='disk'>
<driver name='qemu' type='raw' cache='none'/>
<source file='/disk/sdb6/c1d6.img'/>
<target dev='vdb' bus='virtio'/>
<address type='pci' domain='0x0000' bus='0x00' slot='0x06' function='0x0'/>
</disk>

#保存退出
```

### 重启c1虚拟机

```{bash}

#请使用destroy命令，reboot和shutdown不管用。
~ virsh # destroy c1
Domain c1 destroyed

#list找不到c1 
~ virsh # list
 Id    Name                           State
----------------------------------------------------
 5     server3                        running
 6     server4                        running
 7     d2                             running
 8     r1                             running
 9     server2                        running

#启动虚拟机c1
~ virsh # start c1
Domain c1 started

#进入虚拟机c1
~ console c1
```

### 在c1中，进行硬盘查检并分区

```{bash}

~ sudo fdisk -l

Disk /dev/vda: 42.9 GB, 42949672960 bytes
16 heads, 63 sectors/track, 83220 cylinders, total 83886080 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x000516aa

   Device Boot      Start         End      Blocks   Id  System
/dev/vda1   *        2048      499711      248832   83  Linux
/dev/vda2          501758    83884031    41691137    5  Extended
/dev/vda5          501760    83884031    41691136   8e  Linux LVM

Disk /dev/vdb: 10.7 GB, 10737418240 bytes
16 heads, 63 sectors/track, 20805 cylinders, total 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

Disk /dev/vdb doesn't contain a valid partition table

Disk /dev/mapper/u1210-root: 38.4 GB, 38394658816 bytes
255 heads, 63 sectors/track, 4667 cylinders, total 74989568 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

Disk /dev/mapper/u1210-root doesn't contain a valid partition table

Disk /dev/mapper/u1210-swap_1: 4294 MB, 4294967296 bytes
255 heads, 63 sectors/track, 522 cylinders, total 8388608 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

Disk /dev/mapper/u1210-swap_1 doesn't contain a valid partition table
```

### /dev/vdb已经被识别，接下来 分区,格式化,挂载,使用

### 硬盘分区

```{bash}

~ sudo fdisk /dev/vdb

Command (m for help): p

Disk /dev/vdb: 161.1 GB, 161061274112 bytes
16 heads, 63 sectors/track, 312076 cylinders, total 314572801 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x3b49c6a0

   Device Boot      Start         End      Blocks   Id  System

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1):
Using default value 1
First sector (2048-314572800, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-314572800, default 314572800):
Using default value 314572800

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.

#分区生效
~ sudo partprobe

~ sudo fdisk -l
Disk /dev/vdb: 10.7 GB, 10737418240 bytes
2 heads, 17 sectors/track, 616809 cylinders, total 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0xf0432cd6

   Device Boot      Start         End      Blocks   Id  System
/dev/vdb1            2048    20971519    10484736   83  Linux
```

### 格式化

```{bash}

~ sudo mkfs -t ext4 /dev/vdb1
mke2fs 1.42.5 (29-Jul-2012)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
9830400 inodes, 39321344 blocks
1966067 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=4294967296
1200 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000, 7962624, 11239424, 20480000, 23887872

Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

### 挂载

```{bash}

~ sudo mount /dev/vdb1 /home/cos/hadoopb

~ df -h
Filesystem              Size  Used Avail Use% Mounted on
/dev/mapper/u1210-root   36G  1.1G   33G   4% /
udev                    2.0G  4.0K  2.0G   1% /dev
tmpfs                   791M  232K  791M   1% /run
none                    5.0M     0  5.0M   0% /run/lock
none                    2.0G     0  2.0G   0% /run/shm
none                    100M     0  100M   0% /run/user
/dev/vda1               228M   29M  188M  14% /boot
/dev/vdb1               9.9G  151M  9.2G   2% /home/cos/hadoopb
```

### 使用
/home/cos/hadoopb的目录，已经挂载到了/dev/vdb1上面，我可以在hadoopb下载做任何的操作。

## 3. 通过virsh给guest增加分区硬盘

直接使用HOST的分区硬盘/dev/sdb5，做个虚拟机c1的分区

```{bash}

virsh # edit c1

#新增新硬盘vbc
<disk type='block' device='disk'>
<driver name='qemu' type='raw' cache='none'/>
<source dev='/dev/sdb5'/>
<target dev='vbc' bus='virtio'/>
</disk>

virsh # destroy c1
Domain c1 destroyed

virsh # start c1
Domain c1 started

virsh # console c1
```

### 登陆虚拟c1，查看硬盘信息

```{bash}

sudo fdisk -l
[sudo] password for cos:

Disk /dev/vda: 42.9 GB, 42949672960 bytes
16 heads, 63 sectors/track, 83220 cylinders, total 83886080 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x000516aa

   Device Boot      Start         End      Blocks   Id  System
/dev/vda1   *        2048      499711      248832   83  Linux
/dev/vda2          501758    83884031    41691137    5  Extended
/dev/vda5          501760    83884031    41691136   8e  Linux LVM

Disk /dev/vdb: 10.7 GB, 10737418240 bytes
2 heads, 17 sectors/track, 616809 cylinders, total 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0xf0432cd6

   Device Boot      Start         End      Blocks   Id  System
/dev/vdb1            2048    20971519    10484736   83  Linux

Disk /dev/vdc: 161.1 GB, 161061274112 bytes
4 heads, 4 sectors/track, 19660800 cylinders, total 314572801 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x3b49c6a0

   Device Boot      Start         End      Blocks   Id  System
/dev/vdc1            2048   314572800   157285376+  83  Linux

Disk /dev/mapper/u1210-root: 38.4 GB, 38394658816 bytes
255 heads, 63 sectors/track, 4667 cylinders, total 74989568 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

Disk /dev/mapper/u1210-root doesn't contain a valid partition table

Disk /dev/mapper/u1210-swap_1: 4294 MB, 4294967296 bytes
255 heads, 63 sectors/track, 522 cylinders, total 8388608 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

Disk /dev/mapper/u1210-swap_1 doesn't contain a valid partition table
```

已经被正确识别
Disk /dev/vdc: 161.1 GB, 161061274112 bytes

接下来，可以参考上面类似的过程，分区，格式化，挂载，使用。

### 我们直接使用硬件分区，比分区的映射文件会更有效果。

我的方法可能不是最优的，我只是记录我的一些操作和体会，欢迎同学留言给我建议。

#### 转载请注明出处：http://blog.fens.me/vps-kvm-disk/













