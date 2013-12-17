多硬盘分区管理fdisk
=============

#### ubuntu实用工具系列文章

[操作系统实用工具系列文章](http://blog.fens.me/series-ubuntu/)，将介绍基于Linux ubuntu的各种工具软件的配置和使用。有些工具大家早已耳熟能详，有些工具经常用到但确依然陌生。我将记录我在使用操作系统时，安装及配置工具上面的一些方法，把使用心得记录下来也便于自己的以后查找和回忆。

#### 关于作者
+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/linux-fdisk/

![多硬盘分区管理fdisk](http://blog.fens.me/wp-content/uploads/2013/07/fdisk.png)

#### 前言

硬盘是一台计算机的必备硬件之一，计算机如何开始使用硬盘资源就要从硬盘分区说起。开始时，一台计算机只有一块硬盘，在安装Linux Ubuntu时，第一步就是对硬盘进行分区。当我们已经安装操作系统，硬盘空间不够的时候，我们需要增加新的硬盘扩大使用空间，特别是对于虚拟化的主机，这种增加新的硬盘就变得有一些不一样了，要手动进行处理。

我在做测试的走进了一个误区中，一直对subpartition这个概念不太理解，纠结于sdb2p1,sdb2p1这样的分区命名。经过几次测试后，才发现，问题出现是因为对操作系统的不理解，以至于在互联网上都找找不太多相关问题的讨论。下面我就介绍一下，我的误区及如何找到正确的方法。

#### 目录

1. 分区的基本知识
2. 新增硬盘分区，走入误区
3. 完成对新增硬盘分区及挂载

## 1. 分区的基本知识

### a. 硬盘分类及标识：IDE,SATA,SCSI三个大类

硬盘与标识对应关系如下表所示：

<table>
   <tr>
      <td>装置</td>
      <td>装置在Linux内的文件名</td>
   </tr>
   <tr>
      <td>IDE硬盘机</td>
      <td>/dev/hd[a-d]</td>
   </tr>
   <tr>
      <td>SCSI/SATA/USB硬盘机</td>
      <td>/dev/sd[a-p]</td>
   </tr>
   <tr>
      <td>USB快闪碟</td>
      <td>/dev/sd[a-p](与SATA不同)</td>
   </tr>
   <tr>
      <td>软盘驱动器</td>
      <td>/dev/fd[0-1]</td>
   </tr>
   <tr>
      <td>打印机</td>
      <td>25针: /dev/lp[0-2]</td>
   </tr>
    <tr>
      <td>    </td>
      <td>USB: /dev/usb/lp[0-15]</td>
   </tr>
   <tr>
      <td>鼠标</td>
      <td>PS2: /dev/psaux</td>
   </tr>
   <tr>
      <tr>
      <td>    </td>
      <td>USB: /dev/usb/mouse[0-15]</td>
   </tr>
   </tr>
   <tr>
      <td>当前CDROM/DVDROM</td>
      <td>/dev/cdrom</td>
   </tr>
   <tr>
      <td>当前的鼠标</td>
      <td>/dev/mouse</td>
   </tr>
</table>

上面表格摘自：http://blog.csdn.net/zollty/article/details/7001950

可以看到我现在硬盘有sda标识，物理上是一个SAS硬盘，300G。

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
```

### b. 硬盘构成：磁头(head)，磁道(track)，磁柱(cylinder)，扇区(setor)
在互联网上可以很容易找到解释，我就不细说了。

我们在分区的时修改要能看懂硬盘信息就够了。

```{bash}

Disk /dev/sdb: 1999.3 GB, 1999307276288 bytes
255 heads, 63 sectors/track, 243068 cylinders, total 3904897024 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0xf919a976
```

硬盘信息：
  /dev/sdb，1999.3 GB，255个磁头，63个磁道，243068个磁柱，3904897024个扇区。

### c. 硬盘的分区
关于linux硬盘的分区主要分为 基本分区（primary partion）和扩充分区(extension partion)两种，基本分区和扩充分区的数目之和不能大于4个。基本分区可以直接使用但不能再分区。扩充分区必须再进行二次分区才能使用。需要对扩充分区建立逻辑分区（logical partion），逻辑分区没有数量上限制。

所以，对于新增硬盘来说，如果我们只有4个及以下的分区要求，可以直接用基本分区完成。如果1T的硬盘想分成6个区，就需要用扩充分区+逻辑分区来实现。

## 2. 新增硬盘分区，走入误区

我现在外接了两块SATA 7200转硬盘，分为都是1T的。重新计算机可以看硬盘信息。

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
/dev/sdb2      1952448512  3904892927   976222208    7  HPFS/NTFS/exFAT
```

两块硬盘，分别挂载到了/dev/sdb1，/dev/sdb2。下面我们对/dev/sdb2硬盘进行处理。

### /dev/sdb2还没有进行分区。

```{bash}

~ fdisk -l /dev/sdb2

Disk /dev/sdb2: 999.7 GB, 999651540992 bytes
255 heads, 63 sectors/track, 121534 cylinders, total 1952444416 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

Disk /dev/sdb2 doesn't contain a valid partition table
```

### 注意下面是进入误区的思考：

对sdb2进行磁盘分区规划：

```{bash}
/         50G
/extends  750G
  /ext1   150G
  /ext2   150G
  /ext3   150G
  /ext4   150G
  /ext5   150G
```

### 开始进行分区

```{bash}

~ sudo fdisk  /dev/sdb2
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x6e28e6e6.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): m
Command action
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition
   l   list known partition types
   m   print this menu
   n   add a new partition
   o   create a new empty DOS partition table
   p   print the partition table
   q   quit without saving changes
   s   create a new empty Sun disklabel
   t   change a partition's system id
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit
   x   extra functionality (experts only)
```

创建第一个基本分区/，大小为50G， 50*1024*1024*1024/512+2048=104859648

```{bash}

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1):
Using default value 1
First sector (2048-1952444415, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-1952444415, default 1952444415): 104859648

Command (m for help): p

Disk /dev/sdb2: 999.7 GB, 999651540992 bytes
255 heads, 63 sectors/track, 121534 cylinders, total 1952444416 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x44198dad

     Device Boot      Start         End      Blocks   Id  System
/dev/sdb2p1            2048   104859648    52428800+  83  Linux
```

### 创建第二个扩充分区750G，750*1024*1024*1024/512+104859648=1677723648

```{bash}

Command (m for help): n
Partition type:
   p   primary (1 primary, 0 extended, 3 free)
   e   extended
Select (default p): e
Partition number (1-4, default 2):
Using default value 2
First sector (104859649-1952444415, default 104859649):
Using default value 104859649
Last sector, +sectors or +size{K,M,G} (104859649-1952444415, default 1952444415): 1677723648

Command (m for help): p

Disk /dev/sdb2: 999.7 GB, 999651540992 bytes
255 heads, 63 sectors/track, 121534 cylinders, total 1952444416 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x44198dad

     Device Boot      Start         End      Blocks   Id  System
/dev/sdb2p1            2048   104859648    52428800+  83  Linux
/dev/sdb2p2       104859649  1677723648   786432000    5  Extended
```

### 创建逻辑分区1， 150G， 150*1024*1024*1024/512+104861697=419434497

```{bash}
Command (m for help): n
Partition type:
   p   primary (1 primary, 1 extended, 2 free)
   l   logical (numbered from 5)
Select (default p): l
Adding logical partition 5
First sector (104861697-1677723648, default 104861697):
Using default value 104861697
Last sector, +sectors or +size{K,M,G} (104861697-1677723648, default 1677723648): 419434497
```

+ 创建逻辑分区2， 150G， 150*1024*1024*1024/512+419436546=734009346
+ 创建逻辑分区3， 150G， 150*1024*1024*1024/512+734009346=1048582146
+ 创建逻辑分区4， 150G， 150*1024*1024*1024/512+1048582146=1363154946
+ 创建逻辑分区5， 150G， 150*1024*1024*1024/512+1363154946=1677723648

### 查看分区结果

```{bash}

Command (m for help): p

Disk /dev/sdb2: 999.7 GB, 999651540992 bytes
255 heads, 63 sectors/track, 121534 cylinders, total 1952444416 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x44198dad

     Device Boot      Start         End      Blocks   Id  System
/dev/sdb2p1            2048   104859648    52428800+  83  Linux
/dev/sdb2p2       104859649  1677723648   786432000    5  Extended
/dev/sdb2p5       104861697   419434497   157286400+  83  Linux
/dev/sdb2p6       419436546   734009346   157286400+  83  Linux
/dev/sdb2p7       734011395  1048582146   157285376   83  Linux
/dev/sdb2p8      1048584195  1363154946   157285376   83  Linux
/dev/sdb2p9      1363156995  1677723648   157283327   83  Linux
```

### 退出保存

```{bash}
Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 22: Invalid argument.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
```

### 更新内核分区表
不用重启系统，更新内核分区表

```{bash}
sudo partprobe
```

### 用fdisk查看新建的分区

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
/dev/sdb2      1952448512  3904892927   976222208    7  HPFS/NTFS/exFAT
```

这个时候，相信大家已经发现问题了，新建的分区完全没有生效。

对硬盘sdb2使用fdisk查看分区

```{bash}

~ fdisk -l /dev/sdb2

Disk /dev/sdb2: 999.7 GB, 999651540992 bytes
255 heads, 63 sectors/track, 121534 cylinders, total 1952444416 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x44198dad

     Device Boot      Start         End      Blocks   Id  System
/dev/sdb2p1            2048   104859648    52428800+  83  Linux
/dev/sdb2p2       104859649  1677723648   786432000    5  Extended
/dev/sdb2p5       104861697   419434497   157286400+  83  Linux
/dev/sdb2p6       419436546   734009346   157286400+  83  Linux
/dev/sdb2p7       734011395  1048582146   157285376   83  Linux
/dev/sdb2p8      1048584195  1363154946   157285376   83  Linux
/dev/sdb2p9      1363156995  1677723648   157283327   83  Linux
```

为什么会这样的？网上查了一下，一些朋友也遇到了这样的情况。他们把sdb2p1的这种分区叫做子分区subpartition。

操作系统不明白，为什么要在sdb2的分区上再创建sdb2p1子分区。但当时我也不明白，如果我想把一块新的硬盘分区后再使用应该如何进行。其实，这种纠结的想法，原于对操作系统的不了解，**导致了明白的人不会出现这种错误，出现这种错误的人找不到明白的解释。**

跳出子分区的概念，我们应该如何操作呢！

## 3. 完成对新增硬盘分区及挂载

上面的错误在于，我们不应该对/dev/sdb2硬盘分区，而是对/dev/sdb进行分区。

```{bash}

Disk /dev/sdb: 1999.3 GB, 1999307276288 bytes
255 heads, 63 sectors/track, 243068 cylinders, total 3904897024 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0xf919a976

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048  1952448511   976223232    7  HPFS/NTFS/exFAT
/dev/sdb2      1952448512  3904892927   976222208    7  HPFS/NTFS/exFAT
```

操作系统已经把两块新增加的硬盘，识别为了/dev/sdb，这个时候我们只需要删除原有的/dev/sdb2基本分区，重建为扩充分区，然后再按要求做逻辑分区就可以了。

删除sdb2分区，重建为扩充分区/dev/sdb2

```{bash}

~ sudo fdisk /dev/sdb
Command (m for help): d
Partition number (1-4): 2

Command (m for help): p

Disk /dev/sdb: 1999.3 GB, 1999307276288 bytes
255 heads, 63 sectors/track, 243068 cylinders, total 3904897024 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0xf919a976

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048  1952448511   976223232    7  HPFS/NTFS/exFAT

Command (m for help): n
Partition type:
   p   primary (1 primary, 0 extended, 3 free)
   e   extended
Select (default p): e
Partition number (1-4, default 2):
Using default value 2
First sector (1952448512-3904897023, default 1952448512):
Using default value 1952448512
Last sector, +sectors or +size{K,M,G} (1952448512-3904897023, default 3904897023):
Using default value 3904897023

Command (m for help): p

Disk /dev/sdb: 1999.3 GB, 1999307276288 bytes
255 heads, 63 sectors/track, 243068 cylinders, total 3904897024 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0xf919a976

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048  1952448511   976223232    7  HPFS/NTFS/exFAT
/dev/sdb2      1952448512  3904897023   976224256    5  Extended
```

### 重新规划分区：

+ 创建逻辑分区1， 150G， 150*1024*1024*1024/512+1952450560=2267023360
+ 创建逻辑分区2， 150G， 150*1024*1024*1024/512+2267023360=2581596160
+ 创建逻辑分区3， 150G， 150*1024*1024*1024/512+2581596160=2896168960
+ 创建逻辑分区4， 150G， 150*1024*1024*1024/512+2896168960=3210741760
+ 创建逻辑分区5， 150G， 150*1024*1024*1024/512+3210741760=3525314560
+ 创建逻辑分区6， 189G， 剩余空间

```{bash}

Command (m for help): p

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

### 保存退出

```{bash}
Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
```

### 更新内核分区表

```{bash}
sudo partprobe
```

### 查看新建的分区

```{bash}
ls /dev/sdb
sdb    sdb1   sdb10  sdb2   sdb5   sdb6   sdb7   sdb8   sdb9
```

### 格式化分区

```{bash}
sudo mkfs -t ext4 /dev/sdb5
sudo mkfs -t ext4 /dev/sdb6
sudo mkfs -t ext4 /dev/sdb7
sudo mkfs -t ext4 /dev/sdb8
sudo mkfs -t ext4 /dev/sdb9
sudo mkfs -t ext4 /dev/sdb10
```

### 挂载分区

```{bash}
~ sudo mount -t ext4 /dev/sdb5 /disk/sdb5
~ ls /disk/sdb5
lost+found
```

查看最终的分区效果

```{bash}

cos@delta:~$ sudo fdisk -l
[sudo] password for cos:

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

完成对/dev/sdb2硬盘的分区操作。

希望帮助和我一样走入到误区的朋友吧。

#### 转载请注明出处：http://blog.fens.me/linux-fdisk/


















