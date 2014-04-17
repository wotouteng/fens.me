用Puppet进行系统管理
==========

[ubuntu实用工具系列文章](http://blog.fens.me/series-ubuntu/)，将介绍基于Linux ubuntu的各种工具软件的配置和使用。有些工具大家早已耳熟能详，有些工具经常用到但确依然陌生。我将记录我在使用操作系统时，安装及配置工具上面的一些方法，把使用心得记录下来也便于自己的以后查找和回忆。

#### 关于作者
+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/linux-manage-puppet/

![用Puppet进行系统管理](http://blog.fens.me/wp-content/uploads/2014/03/linux-puppet.png)

#### 前言

系统运维本是一件枯燥，而且重复性强的工作，不停地搞命令占据了运维人员的大量时间。Puppet通过一种集中式管理的设计，让运维变得简单可控。管理员只需要在master节点修改配置，绑定的客户端节点就会自动同步配置，让命令行也能通过配置实现。

第一次尝试，从安装开始。

#### 目录

1. Puppet是什么？
2. Puppet服务器安装及配置
3. Puppet客户端安装及配置
4. 注册puppet客户端到服务器端
5. 例子：生成文件测试

## 1. Puppet是什么？

puppet是一种Linux、Unix、windows平台的集中配置管理系统，使用自有的puppet描述语言，可管理配置文件、用户、cron任务、软件包、系统服务等。puppet把这些系统实体称之为资源，puppet的设计目标是简化对这些资源的管理以及妥善处理资源间的依赖关系。

puppet采用C/S星状的结构，所有的客户端和一个或几个服务器交互。每个客户端周期的（默认半个小时）向服务器发送请求，获得其最新的配置信息，保证和该配置信息同步。每个puppet客户端每半小时(可以设置)连接一次服务器端, 下载最新的配置文件,并且严格按照配置文件来配置服务器. 配置完成以后,puppet客户端可以反馈给服务器端一个消息. 如果出错,也会给服务器端反馈一个消息.

**2台服务器的环境配置：**

Master Server：

* Linux Ubuntu 12.04.2 LTS 64bit
* ip: 192.168.1.201
* hostname: vm1
* ruby: 1.8.7

Client Server：

* Linux Ubuntu 12.04.2 LTS 64bit
* ip: 192.168.1.202
* hostname: vm2
* ruby: 1.8.7

## 2. Puppet服务器安装及配置

* 切换到root用户
* 设置host
* 安装Puppet服务器端

切换到root用户

```{bash}
~ sudo -i
~ whoami
root
```

### 1). 设置host

```{bash}
~ hostname vm1
~ hostanme
vm1

~ vi /etc/hosts
127.0.0.1       localhost
192.168.1.201   vm1
192.168.1.202   vm2
```

### 2). 安装Puppet服务器端

```{bash}
#安装并启动Puppet服务器端
~ apt-get install puppetmaster

#检查端口8140
~ netstat -nlt |grep 8140
tcp        0      0 0.0.0.0:8140            0.0.0.0:*               LISTEN
   
#查看Puppet的版本
~ puppet -V
2.7.11

#启动Puppet服务器
~  puppet master --verbose --no-daemonize
notice: Starting Puppet master version 2.7.11
```

### 3). 配置Puppet服务器，在[master]标签下，增加certname设置

```{bash}
~ vi /etc/puppet/puppet.conf

[master]
certname=vm1 #增加服务器配置

#重启Puppet服务器
~ /etc/init.d/puppetmaster restart
* Restarting puppet master 
```

### 4). 查看Puppet服务器的证书

```{bash}
~ puppet cert --list --all
+ "vm1" (60:0E:1D:8F:80:0B:5D:7D:F8:8B:C7:C6:DF:CF:69:43)
```

"+" 为已注册的证书

这样我们就完成了，服务器端的配置！接下来，继续进行客户端的配置。

## 3. Puppet客户端安装及配置

切换到root用户

```{bash}
~ sudo -i
~ whoami
root
```

### 2).设置host

```{bash}
~ hostname vm2
~ hostanme
vm2

~ vi /etc/hosts
127.0.0.1       localhost
192.168.1.201   vm1
192.168.1.202   vm2
```

### 2). 安装Puppet服务器端

```{bash}
#安装并启动Puppet客户端
~ apt-get install puppet
   
#查看Puppet的版本
~ puppet -V
2.7.11
```

### 3). 配置Puppet客户端，在[agent]标签下增加server设置

```{bash}
~ sudo vi /etc/puppet/puppet.conf

[agent]
server=vm1
```

### 4). 配置Puppet客户端default值，修改START设置

```{bash}
~ vi /etc/default/puppet

START=yes
```

设置START把no改为yes

重启Puppet客户端

```{bash}

~ /etc/init.d/puppet restart
* Restarting puppet agent
```

## 4. 注册puppet客户端到服务器端

### 1). 检查服务器端的证书

```{bash}
#服务器端操作
~ puppet cert --list --all
+ "vm1" (60:0E:1D:8F:80:0B:5D:7D:F8:8B:C7:C6:DF:CF:69:43)
```

### 2). 在服务器端(vm1)创建文件site.pp

```{bash}
#服务器端操作
~ sudo vi /etc/puppet/manifests/site.pp

node default {
  notify { "Hey ! It works !": }
}
```

### 3). 从客户端(vm2)向服务器端发起注册请求

```{bash}
#客户端操作
~ puppet agent -t
info: Creating a new SSL key for vm2
info: Caching certificate for ca
info: Creating a new SSL certificate request for vm2
info: Certificate Request fingerprint (md5): 15:45:D1:22:1B:97:6D:49:43:BC:93:D8:BB:4C:E1:99
Exiting; no certificate found and waitforcert is disabled

#如果之前生成发启过认证请求，则需要删除证书的目录
~ rm -rf /var/lib/puppet/ssl
~ puppet agent -t
```

### 4). 服务器端(vm1)查看客户端请求认证的列表

```{bash}
#服务器端操作
~ puppet cert --list
  "vm2" (15:45:D1:22:1B:97:6D:49:43:BC:93:D8:BB:4C:E1:99)
```

### 5). 服务器端(vm1)接受客户端的请求

```{bash}
#服务器端操作
~ puppet cert --sign vm2
notice: Signed certificate request for vm2
notice: Removing file Puppet::SSL::CertificateRequest vm2 at '/var/lib/puppet/ssl/ca/requests/vm2.pem'

#可以接受所有的请求
~ puppet cert -s -a

#查看认证后，查看服务器上公钥的位置
~ sudo ls  /var/lib/puppet/ssl/ca/signed/
vm1.pem  vm2.pem
```

### 6). 服务器端(vm1)查看的证书列表

```{bash}
#服务器端操作
~ puppet cert --list --all
+ "vm1" (B7:45:28:1B:AE:53:0B:D7:38:8E:66:70:6C:B8:13:A4)
+ "vm2" (FB:64:C7:38:DA:B4:C7:0B:E6:3B:85:86:9A:F4:61:C2)
```

### 7). 客户端(vm2)执行服务器设置的脚本site.pp

```{bash}
#客户端操作
~ puppetd --test
info: Caching certificate for vm2
info: Caching certificate_revocation_list for ca
info: Caching catalog for vm2
info: Applying configuration version '1383527011'
notice: Hey ! It works !
notice: /Stage[main]//Node[default]/Notify[Hey ! It works !]/message: defined 'message' as 'Hey ! It works !'
notice: Finished catalog run in 0.02 seconds
```

我们看到’Hey ! It works !’，被正常显示出来。

## 5. 例子：生成文件测试

### 1). 生成文件测试，在/tmp目录下生成一个文件hello.txt。

在服务器端修改一个文件site.pp

```{bash}
~ vi /etc/puppet/manifests/hello.pp

node default {
  notify { "Hey ! It works !": }
  file {
    "/tmp/hello.txt": content => "hello world";
  }
}
```

### 2). 在服务器端运行hello.pp文件

```{bash}
~ puppet apply /etc/puppet/manifests/site.pp
warning: Could not retrieve fact fqdn
warning: Host is missing hostname and/or domain: vm1
notice: /Stage[main]//Node[default]/File[/tmp/hello.txt]/ensure: defined content as '{md5}5eb63bbbe01eeed093cb22bb8f5acdc3'
notice: Hey ! It works !
notice: /Stage[main]//Node[default]/Notify[Hey ! It works !]/message: defined 'message' as 'Hey ! It works !'
notice: Finished catalog run in 0.02 seconds

#检查生成的文件
~ cat /tmp/hello.txt
hello world
```

### 3). 在客户端同步

```{bash}
~  puppet agent --test --server=vm1
info: Caching catalog for vm2
info: Applying configuration version '1383528346'
notice: /Stage[main]//Node[default]/File[/tmp/hello.txt]/ensure: defined content as '{md5}5eb63bbbe01eeed093cb22bb8f5acdc3'
notice: Hey ! It works !
notice: /Stage[main]//Node[default]/Notify[Hey ! It works !]/message: defined 'message' as 'Hey ! It works !'
notice: Finished catalog run in 0.07 seconds

#检查生成的文件
~ cat /tmp/hello.txt
hello world
```

这样我们就实现了第一个最简单的利用Puppet自动化运维的例子。出步尝试已经成功，接下来就像要深入研究Puppet到底能干什么，实现系统的高效运维。

#### 转载请注明出处：http://blog.fens.me/linux-manage-puppet/

