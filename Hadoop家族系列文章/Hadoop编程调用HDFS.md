Hadoop编程调用HDFS
============

[Hadoop家族系列文章](http://blog.fens.me/series-hadoop-family/)，主要介绍Hadoop家族产品，常用的项目包括Hadoop, Hive, Pig, HBase, Sqoop, Mahout, Zookeeper, Avro, Ambari, Chukwa，新增加的项目包括，YARN, Hcatalog, Oozie, Cassandra, Hama, Whirr, Flume, Bigtop, Crunch, Hue等。

从2011年开始，中国进入大数据风起云涌的时代，以Hadoop为代表的家族软件，占据了大数据处理的广阔地盘。开源界及厂商，所有数据软件，无一不向Hadoop靠拢。Hadoop也从小众的高富帅领域，变成了大数据开发的标准。在Hadoop原有技术基础之上，出现了Hadoop家族产品，通过“大数据”概念不断创新，推出科技进步。

作为IT界的开发人员，我们也要跟上节奏，抓住机遇，跟着Hadoop一起雄起！

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/hadoop-hdfs-api/

![Hadoop编程调用HDFS](http://blog.fens.me/wp-content/uploads/2013/10/hadoop-hdfs-api.png)

#### 前言

HDFS 全称Hadoop分步文件系统(Hadoop Distributed File System)，是Hadoop的核心部分之一。要实现MapReduce的分步式算法时，数据必需提前放在HDFS上。因此，对于HDFS的操作就变得非常重要。Hadoop的命令行，提供了一套完整命令接口，就像Linux命令一样方便使用。

不过，有时候我们还需要在程序中直接访问HDFS，我们可以通过API的方式进行HDFS操作。

#### 目录

1. 系统环境
2. ls操作
3. rmr操作
4. mkdir操作
5. copyFromLocal操作
6. cat操作
7. copyToLocal操作
8. 创建一个新文件，并写入内容

## 1. 系统环境

### Hadoop集群环境

* Linux Ubuntu 64bit Server 12.04.2 LTS
* Java 1.6.0_29
* Hadoop 1.1.2

如何搭建Hadoop集群环境？ 请参考文章：Hadoop历史版本安装

### 开发环境

* Win7 64bit
* Java 1.6.0_45
* Maven 3
* Hadoop 1.1.2
* Eclipse Juno Service Release 2

如何用Maven搭建Win7的Hadoop开发环境？ 请参考文章：[用Maven构建Hadoop项目](http://blog.fens.me/hadoop-maven-eclipse/)

注：hadoop-core-1.1.2.jar，已重新编译，已解决了Win远程调用Hadoop的问题，请参考文章：[Hadoop历史版本安装](http://blog.fens.me/hadoop-history-source-install/)

Hadooop命令行：java FsShell

```{bash}

~ hadoop fs

Usage: java FsShell
           [-ls ]
           [-lsr ]
           [-du ]
           [-dus ]
           [-count[-q] ]
           [-mv  ]
           [-cp  ]
           [-rm [-skipTrash] ]
           [-rmr [-skipTrash] ]
           [-expunge]
           [-put  ... ]
           [-copyFromLocal  ... ]
           [-moveFromLocal  ... ]
           [-get [-ignoreCrc] [-crc]  ]
           [-getmerge   [addnl]]
           [-cat ]
           [-text ]
           [-copyToLocal [-ignoreCrc] [-crc]  ]
           [-moveToLocal [-crc]  ]
           [-mkdir ]
           [-setrep [-R] [-w]  ]
           [-touchz ]
           [-test -[ezd] ]
           [-stat [format] ]
           [-tail [-f] ]
           [-chmod [-R]  PATH...]
           [-chown [-R] [OWNER][:[GROUP]] PATH...]
           [-chgrp [-R] GROUP PATH...]
           [-help [cmd]]
```

上面列出了30个命令，我只实现了一部分的HDFS的命令！

新建文件：HdfsDAO.java，用来调用HDFS的API。

```{bash}

public class HdfsDAO {

    //HDFS访问地址
    private static final String HDFS = "hdfs://192.168.1.210:9000/";

    public HdfsDAO(Configuration conf) {
        this(HDFS, conf);
    }

    public HdfsDAO(String hdfs, Configuration conf) {
        this.hdfsPath = hdfs;
        this.conf = conf;
    }

    //hdfs路径
    private String hdfsPath;
    //Hadoop系统配置
    private Configuration conf;

    //启动函数
    public static void main(String[] args) throws IOException {
        JobConf conf = config();
        HdfsDAO hdfs = new HdfsDAO(conf);
        hdfs.mkdirs("/tmp/new/two");
        hdfs.ls("/tmp/new");
    }        
    
    //加载Hadoop配置文件
    public static JobConf config(){
        JobConf conf = new JobConf(HdfsDAO.class);
        conf.setJobName("HdfsDAO");
        conf.addResource("classpath:/hadoop/core-site.xml");
        conf.addResource("classpath:/hadoop/hdfs-site.xml");
        conf.addResource("classpath:/hadoop/mapred-site.xml");
        return conf;
    }

    //API实现
    public void cat(String remoteFile) throws IOException {...}
    public void mkdirs(String folder) throws IOException {...}
    
    ...
}
```

## 2. ls操作

说明：查看目录文件

对应Hadoop命令：

```{bash}
~ hadoop fs -ls /
Found 3 items
drwxr-xr-x   - conan         supergroup          0 2013-10-03 05:03 /home
drwxr-xr-x   - Administrator supergroup          0 2013-10-03 13:49 /tmp
drwxr-xr-x   - conan         supergroup          0 2013-10-03 09:11 /user
```

## 2. ls操作

说明：查看目录文件

对应Hadoop命令：

```{bash}
~ hadoop fs -ls /
Found 3 items
drwxr-xr-x   - conan         supergroup          0 2013-10-03 05:03 /home
drwxr-xr-x   - Administrator supergroup          0 2013-10-03 13:49 /tmp
drwxr-xr-x   - conan         supergroup          0 2013-10-03 09:11 /user
```

Java程序:

```{bash}
    public void ls(String folder) throws IOException {
        Path path = new Path(folder);
        FileSystem fs = FileSystem.get(URI.create(hdfsPath), conf);
        FileStatus[] list = fs.listStatus(path);
        System.out.println("ls: " + folder);
        System.out.println("==========================================================");
        for (FileStatus f : list) {
            System.out.printf("name: %s, folder: %s, size: %d\n", f.getPath(), f.isDir(), f.getLen());
        }
        System.out.println("==========================================================");
        fs.close();
    }

    public static void main(String[] args) throws IOException {
        JobConf conf = config();
        HdfsDAO hdfs = new HdfsDAO(conf);
        hdfs.ls("/");
    }   
```

控制台输出：

```{bash}
ls: /
==========================================================
name: hdfs://192.168.1.210:9000/home, folder: true, size: 0
name: hdfs://192.168.1.210:9000/tmp, folder: true, size: 0
name: hdfs://192.168.1.210:9000/user, folder: true, size: 0
==========================================================
```

## 3. mkdir操作

说明：创建目录，可以创建多级目录

对应Hadoop命令：

```{bash}
~ hadoop fs -mkdir /tmp/new/one
~ hadoop fs -ls /tmp/new
Found 1 items
drwxr-xr-x   - conan supergroup          0 2013-10-03 15:35 /tmp/new/one
```

Java程序:

```{bash}
    public void mkdirs(String folder) throws IOException {
        Path path = new Path(folder);
        FileSystem fs = FileSystem.get(URI.create(hdfsPath), conf);
        if (!fs.exists(path)) {
            fs.mkdirs(path);
            System.out.println("Create: " + folder);
        }
        fs.close();
    }

    public static void main(String[] args) throws IOException {
        JobConf conf = config();
        HdfsDAO hdfs = new HdfsDAO(conf);
        hdfs.mkdirs("/tmp/new/two");
        hdfs.ls("/tmp/new");
    }   
```

控制台输出：

```{bash}
Create: /tmp/new/two
ls: /tmp/new
==========================================================
name: hdfs://192.168.1.210:9000/tmp/new/one, folder: true, size: 0
name: hdfs://192.168.1.210:9000/tmp/new/two, folder: true, size: 0
==========================================================
```

## 4. rmr操作

说明：删除目录和文件

对应Hadoop命令：

```{bash}
~ hadoop fs -rmr /tmp/new/one
Deleted hdfs://master:9000/tmp/new/one

~  hadoop fs -ls /tmp/new
Found 1 items
drwxr-xr-x   - Administrator supergroup          0 2013-10-03 15:38 /tmp/new/two
```

Java程序:

```{bash}
    public void rmr(String folder) throws IOException {
        Path path = new Path(folder);
        FileSystem fs = FileSystem.get(URI.create(hdfsPath), conf);
        fs.deleteOnExit(path);
        System.out.println("Delete: " + folder);
        fs.close();
    }

    public static void main(String[] args) throws IOException {
        JobConf conf = config();
        HdfsDAO hdfs = new HdfsDAO(conf);
        hdfs.rmr("/tmp/new/two");
        hdfs.ls("/tmp/new");
    }     
```

控制台输出：

```{bash}
Delete: /tmp/new/two
ls: /tmp/new
==========================================================
==========================================================
```

## 5. copyFromLocal操作

说明：复制本地文件系统到HDFS

对应Hadoop命令：

```{bash}
~ hadoop fs -copyFromLocal /home/conan/datafiles/item.csv /tmp/new/

~ hadoop fs -ls /tmp/new/
Found 1 items
-rw-r--r--   1 conan supergroup        210 2013-10-03 16:07 /tmp/new/item.csv
```

Java程序:

```{bash}
    public void copyFile(String local, String remote) throws IOException {
        FileSystem fs = FileSystem.get(URI.create(hdfsPath), conf);
        fs.copyFromLocalFile(new Path(local), new Path(remote));
        System.out.println("copy from: " + local + " to " + remote);
        fs.close();
    }

    public static void main(String[] args) throws IOException {
        JobConf conf = config();
        HdfsDAO hdfs = new HdfsDAO(conf);
        hdfs.copyFile("datafile/randomData.csv", "/tmp/new");
        hdfs.ls("/tmp/new");
    }    
```

控制台输出：

```{bash}
copy from: datafile/randomData.csv to /tmp/new
ls: /tmp/new
==========================================================
name: hdfs://192.168.1.210:9000/tmp/new/item.csv, folder: false, size: 210
name: hdfs://192.168.1.210:9000/tmp/new/randomData.csv, folder: false, size: 36655
==========================================================
```

## 6. cat操作

说明：查看文件内容

对应Hadoop命令：

```{bash}
~ hadoop fs -cat /tmp/new/item.csv
1,101,5.0
1,102,3.0
1,103,2.5
2,101,2.0
2,102,2.5
2,103,5.0
2,104,2.0
3,101,2.5
3,104,4.0
3,105,4.5
3,107,5.0
4,101,5.0
4,103,3.0
4,104,4.5
4,106,4.0
5,101,4.0
5,102,3.0
5,103,2.0
5,104,4.0
5,105,3.5
5,106,4.0
```

Java程序:

```{bash}
    public void cat(String remoteFile) throws IOException {
        Path path = new Path(remoteFile);
        FileSystem fs = FileSystem.get(URI.create(hdfsPath), conf);
        FSDataInputStream fsdis = null;
        System.out.println("cat: " + remoteFile);
        try {  
            fsdis =fs.open(path);
            IOUtils.copyBytes(fsdis, System.out, 4096, false);  
          } finally {  
            IOUtils.closeStream(fsdis);
            fs.close();
          }
    }

    public static void main(String[] args) throws IOException {
        JobConf conf = config();
        HdfsDAO hdfs = new HdfsDAO(conf);
        hdfs.cat("/tmp/new/item.csv");
    } 
```

控制台输出：

```{bash}
cat: /tmp/new/item.csv
1,101,5.0
1,102,3.0
1,103,2.5
2,101,2.0
2,102,2.5
2,103,5.0
2,104,2.0
3,101,2.5
3,104,4.0
3,105,4.5
3,107,5.0
4,101,5.0
4,103,3.0
4,104,4.5
4,106,4.0
5,101,4.0
5,102,3.0
5,103,2.0
5,104,4.0
5,105,3.5
5,106,4.0
```

## 7. copyToLocal操作

说明：从HDFS复制文件在本地操作系

对应Hadoop命令：

```{bash}
~ hadoop fs -copyToLocal /tmp/new/item.csv /home/conan/datafiles/tmp/

~ ls -l /home/conan/datafiles/tmp/
-rw-rw-r-- 1 conan conan 210 Oct  3 16:16 item.csv
```

Java程序:

```{bash}
    public void download(String remote, String local) throws IOException {
        Path path = new Path(remote);
        FileSystem fs = FileSystem.get(URI.create(hdfsPath), conf);
        fs.copyToLocalFile(path, new Path(local));
        System.out.println("download: from" + remote + " to " + local);
        fs.close();
    }

    public static void main(String[] args) throws IOException {
        JobConf conf = config();
        HdfsDAO hdfs = new HdfsDAO(conf);
        hdfs.download("/tmp/new/item.csv", "datafile/download");
        
        File f = new File("datafile/download/item.csv");
        System.out.println(f.getAbsolutePath());
    }    
```

控制台输出：

```{bash}
2013-10-12 17:17:32 org.apache.hadoop.util.NativeCodeLoader 
警告: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
download: from/tmp/new/item.csv to datafile/download
D:\workspace\java\myMahout\datafile\download\item.csv
```

## 8. 创建一个新文件，并写入内容

说明：创建一个新文件，并写入内容。

* touchz:可以用来创建一个新文件，或者修改文件的时间戳。
* 写入内容没有对应命令。

对应Hadoop命令：

```{bash}

~ hadoop fs -touchz /tmp/new/empty

~ hadoop fs -ls /tmp/new
Found 3 items
-rw-r--r--   1 conan         supergroup          0 2013-10-03 16:24 /tmp/new/empty
-rw-r--r--   1 conan         supergroup        210 2013-10-03 16:07 /tmp/new/item.csv
-rw-r--r--   3 Administrator supergroup      36655 2013-10-03 16:09 /tmp/new/randomData.csv

~ hadoop fs -cat /tmp/new/empty
```

Java程序:

```{bash}
    public void createFile(String file, String content) throws IOException {
        FileSystem fs = FileSystem.get(URI.create(hdfsPath), conf);
        byte[] buff = content.getBytes();
        FSDataOutputStream os = null;
        try {
            os = fs.create(new Path(file));
            os.write(buff, 0, buff.length);
            System.out.println("Create: " + file);
        } finally {
            if (os != null)
                os.close();
        }
        fs.close();
    }

    public static void main(String[] args) throws IOException {
        JobConf conf = config();
        HdfsDAO hdfs = new HdfsDAO(conf);
        hdfs.createFile("/tmp/new/text", "Hello world!!");
        hdfs.cat("/tmp/new/text");
    }   
```

控制台输出：

```{bash}
Create: /tmp/new/text
cat: /tmp/new/text
Hello world!!
```

#### 完整的文件：HdfsDAO.java
https://github.com/bsspirit/maven_mahout_template/blob/mahout-0.8/src/main/java/org/conan/mymahout/hdfs/HdfsDAO.java

#### 转载请注明出处：http://blog.fens.me/hadoop-hdfs-api/

