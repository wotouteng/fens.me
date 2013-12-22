网络问题 – bitbucket同步数据
=============

[自己搭建VPS系列文章](http://blog.fens.me/series-vps/)，介绍了如何利用自己的计算机资源，通过虚拟化技术搭建VPS。

在互联网2.0时代，每个人都有自己的博客，还有很多专属于自己的互联网应用。这些应用大部分都是互联网公司提供的。对于一些有能力的开发人员(geek)来说，他们希望做一些自己的应用，可以用到最新最炫的技术，并且有自己的域名，有自己的服务器。这时就要去租一些互联网上的VPS主机。VPS主机就相当于是一台远程的计算机，可以部署自己的应用程序，然后申请一个域名，就可以正式发布在互联网上了。本站“[@晒粉丝](http://www.fens.me/)” 就使用的[Linode主机](http://www.linode.com/?r=70e684fdc20f965f06b7b6e80f6acfd25db211ec)VPS在美国达拉斯机房。

其实，VPS还可以自己搭建的。只要我们有一台高性能的服务器，一个IP地址，一个路由。可以把一台高性能的服务器，很快的变成5台，10台，20台的虚拟VPS。我们就可以在自己的VPS上面的，发布各种的应用，还可以把剩余的服务器资源租给其他的互联网使用者。 本系列文章将分为以下几个部分介绍：“虚拟化技术选型”，[“动态IP解析”](http://blog.fens.me/vps-ip-dns)，[“在Ubuntu上安装KVM并搭建虚拟环境”](http://blog.fens.me/vps-kvm/)，[“给KVM虚拟机增加硬盘”](http://blog.fens.me/vps-kvm-disk)，[“Nova安装攻略”](http://blog.fens.me/vps-nova-setup)，“VPS内网的网络架构设计”，“VPS租用云服务”。

#### 关于作者：

+ 张丹(Conan), 程序员Java,R,PHP,Javacript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/vps-network-error/

![网络问题 – bitbucket同步数据](http://blog.fens.me/wp-content/uploads/2013/06/bitbucket-title.png)

#### 前言

话要从bitbucket.org被封说起。github众所周知，是IT界最有名的代码开放社区，2013年1月间由于12306的订票助手使用到了github上面分享的代码，github.com网站先被拖垮，后被封杀，Github被GFW屏蔽，最后IT界的所有大老们通过社会舆论的影响，才使得github逃过GFW的劫难。

bitbucket.org同样也是代码开放社区，大概于2013年4月的一天悄悄被GFW封了。很多朋友的私有项目，都是托管在bitbucket.org社区的，这样一封又是一场不小的灾难。

#### 目录

1. 问题描述
2. ping测试
3. dig测试
4. traceroute测试
5. tracepath测试
6. 未解决的问题1 – 为什么显示星号(*)和no reply
7. 未解决的问题2 – bitbucket.org同步数据

## 1. 问题描述

我们今天要讲的内容，虽然由bitbucket引起，但确是网络技术的问题。

为了创业做准备，朋友在深圳买了一台服务器放在家里，网络服务商为”深圳电信”。 服务器IP每天会变的问题，已经解决：[自己搭建VPS系列文章 之 动态IP解析](http://blog.fens.me/vps-ip-dns/)。

我们使用GIT通过bitbucket.org社区管理代码。经常会出现的现象就是，git clone / git pull/ git push 的操作时，长时间的等待代码不能同步。但有时候，“运气”好又可以同步代码。

## 2. ping测试

linux ubuntu环境中，用ping去检查是不是连接正常。

下面是正常的ping连接, 207.223.240.182，ping的速度慢但比较稳定。

```{bash}

cos@delta:~$ ping bitbucket.org
PING bitbucket.org (207.223.240.182) 56(84) bytes of data.
64 bytes from 207.223.240.182: icmp_req=1 ttl=47 time=224 ms
64 bytes from 207.223.240.182: icmp_req=2 ttl=47 time=224 ms
64 bytes from 207.223.240.182: icmp_req=3 ttl=47 time=224 ms
```

不正常的ping连接，207.223.240.181，ping的过程，经常是长时间等待，完全连不通。

```{bash}
cos@delta:~$ ping bitbucket.org
PING bitbucket.org (207.223.240.181) 56(84) bytes of data.
```

## 3. dig测试

为什么会有两个IP，我们查看一下DNS解析。

```{bash}
 cos@delta:~$ dig bitbucket.org

; <<>> DiG 9.8.1-P1 <<>> bitbucket.org
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 29646
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 13, ADDITIONAL: 0

;; QUESTION SECTION:
;bitbucket.org. IN A

;; ANSWER SECTION:
bitbucket.org. 5351 IN A 207.223.240.181
bitbucket.org. 5351 IN A 207.223.240.182

;; AUTHORITY SECTION:
. 4447 IN NS e.root-servers.net.
. 4447 IN NS j.root-servers.net.
. 4447 IN NS a.root-servers.net.
. 4447 IN NS f.root-servers.net.
. 4447 IN NS b.root-servers.net.
. 4447 IN NS g.root-servers.net.
. 4447 IN NS m.root-servers.net.
. 4447 IN NS c.root-servers.net.
. 4447 IN NS h.root-servers.net.
. 4447 IN NS d.root-servers.net.
. 4447 IN NS l.root-servers.net.
. 4447 IN NS i.root-servers.net.
. 4447 IN NS k.root-servers.net.

;; Query time: 1 msec
;; SERVER: 192.168.1.7#53(192.168.1.7)
;; WHEN: Sat Jun 29 20:15:02 2013
;; MSG SIZE rcvd: 274
```

确实发现bitbucket.org域名绑定了2个IP。

## 4. traceroute测试

接下来，跟踪路由跳转的情况。**一测试才发现傻眼了。**

```{bash}
cos@delta:~$ traceroute bitbucket.org
traceroute to bitbucket.org (207.223.240.181), 30 hops max, 60 byte packets
 1  192.168.1.1 (192.168.1.1)  0.432 ms  0.427 ms  0.421 ms
 2  * * *
 3  * * *
 4  * * *
 5  * * *
 6  * * *
 7  * * *
 8  * * *
 9  * * *
10  * * *
11  * * *
12  * * *
13  * * *
14  * * *
15  * * *
16  * * *
17  * * *
18  * * *
19  * * *
20  * * *
21  * * *
22  * * *
23  * * *
24  * * *
25  * * *
26  * * *
27  * * *
28  * * *
29  * * *
30  * * *
```

**都是*，这说明什么呢？难道全部都超时？！**

测试一下其他的域名，同样的问题。

```{bash}
cos@delta:~$ traceroute fens.me
traceroute to fens.me (50.116.27.194), 30 hops max, 60 byte packets
 1  192.168.1.1 (192.168.1.1)  0.405 ms  0.403 ms  0.397 ms
 2  * * *
 3  * * *
 4  * * *
 5  * * *
 6  * * *
```

有人说这个问题是因为traceroute默认使用udp协议造成的，那我们改用icmp协议看看结果。

```{bash}
cos@delta:~$ sudo traceroute -I bitbucket.org
traceroute to bitbucket.org (207.223.240.181), 30 hops max, 60 byte packets
 1  192.168.1.1 (192.168.1.1)  0.349 ms  0.361 ms *
 2  * * *
 3  * * *
 4  * * *
 5  * * *
 6  * * *
 7  * * *
 8  * * *
 9  * * *
10  * * *
11  * * *
12  * * *
13  * * *
14  * * *
15  * * *
16  * * *
17  * * *
18  207.223.240.181 (207.223.240.181)  226.251 ms  226.787 ms  227.130 ms
```

还是*，完全不是我们预期的情况。

### 我们换台机器再测试一下：

阿里云租的服务器：

```{bash}
root@AY121111030241cda8003:~# traceroute bitbucket.org
traceroute to bitbucket.org (207.223.240.182), 30 hops max, 60 byte packets
 1  ip223.hichina.com (223.4.215.252)  0.506 ms  0.629 ms  0.788 ms
 2  10.200.252.4 (10.200.252.4)  0.567 ms 10.200.252.8 (10.200.252.8)  0.473 ms                            10.200.252.4 (10.200.252.4)  0.651 ms
 3  121.0.31.26 (121.0.31.26)  0.729 ms  0.661 ms 119.38.221.90 (119.38.221.90)                             0.539 ms
 4  119.38.220.70 (119.38.220.70)  26.691 ms 119.38.220.138 (119.38.220.138)  0.                           782 ms 119.38.220.188 (119.38.220.188)  1.049 ms
 5  42.120.252.138 (42.120.252.138)  2.086 ms 42.120.252.150 (42.120.252.150)  3                           .812 ms 42.120.253.162 (42.120.253.162)  15.056 ms
 6  122.224.213.214 (122.224.213.214)  0.748 ms 122.224.187.18 (122.224.187.18)                             0.764 ms  0.880 ms
 7  61.164.13.77 (61.164.13.77)  4.193 ms 61.164.13.129 (61.164.13.129)  3.156 m                           s 220.191.135.101 (220.191.135.101)  2.771 ms
 8  202.97.55.9 (202.97.55.9)  3.548 ms 202.97.68.137 (202.97.68.137)  4.277 ms 202.97.55.5 (202.97.55.5)  3.498 ms
 9  202.97.50.254 (202.97.50.254)  4.065 ms 202.97.50.246 (202.97.50.246)  4.896 ms  4.562 ms
10  202.97.35.94 (202.97.35.94)  102.816 ms 202.97.35.18 (202.97.35.18)  6.412 ms 202.97.35.94 (202.97.35.94)  101.986 ms
11  202.97.50.114 (202.97.50.114)  138.102 ms 202.97.58.186 (202.97.58.186)  198.226 ms  198.229 ms
12  202.97.52.226 (202.97.52.226)  179.517 ms 202.97.90.38 (202.97.90.38)  157.887 ms  157.835 ms
13  xe-9-2-3.edge2.SanJose3.Level3.net (4.53.210.113)  187.224 ms  187.255 ms  166.808 ms
14  vlan80.csw3.SanJose1.Level3.net (4.69.152.190)  209.114 ms  187.905 ms vlan70.csw2.SanJose1.Level3.net (4.69.152.126)  213.411 ms
15  ae-62-62.ebr2.SanJose1.Level3.net (4.69.153.17)  239.783 ms ae-92-92.ebr2.SanJose1.Level3.net (4.69.153.29)  251.184 ms ae-82-82.ebr2.SanJose1.Level3.net (4.69.153.25)  178.626 ms
16  ae-3-3.ebr1.Denver1.Level3.net (4.69.132.58)  389.061 ms  389.349 ms  371.320 ms
17  ae-1-100.ebr2.Denver1.Level3.net (4.69.151.182)  214.218 ms  214.161 ms  200.663 ms
18  ae-4-4.car1.StLouis1.Level3.net (4.69.132.181)  238.644 ms  217.050 ms  216.909 ms
19  XIOLINK-LLC.car1.StLouis1.Level3.net (4.53.160.186)  201.729 ms  201.835 ms  201.292 ms
20  b1-e21.c1-f00.stl1.rackmy.com (63.246.15.2)  238.787 ms  215.672 ms  243.613 ms
21  * * *
```

### 北京联通(win7)：

```{bash}
C:\Users\Administrator>tracert bitbucket.org

通过最多 30 个跃点跟踪
到 bitbucket.org [207.223.240.182] 的路由:

  1     3 ms     1 ms     1 ms  PC201304202140 [192.168.1.1]
  2    12 ms     8 ms    12 ms  111.192.144.1
  3     6 ms     2 ms     5 ms  61.148.185.69
  4     4 ms     2 ms     3 ms  124.65.61.137
  5     4 ms     7 ms     3 ms  123.126.0.169
  6    46 ms    42 ms    43 ms  219.158.100.158
  7    79 ms    77 ms    77 ms  219.158.11.206
  8    69 ms    69 ms    71 ms  219.158.97.2
  9   220 ms   219 ms   218 ms  sl-st30-sj-0-4-3-1.sprintlink.net [144.228.110.93]
 10   206 ms   206 ms   207 ms  144.232.12.151
 11   202 ms   200 ms   197 ms  sl-xocomm-337432-0.sprintlink.net [144.223.1.2]
 12   249 ms   251 ms   239 ms  207.88.14.225.ptr.us.xo.net [207.88.14.225]
 13   248 ms   251 ms   253 ms  te-3-0-0.rar3.denver-co.us.xo.net [207.88.12.57]
 14   244 ms   243 ms   248 ms  ae0d0.mcr2.marylandheights-mo.us.xo.net [216.156.0.182]
 15   240 ms   242 ms   239 ms  ae1d0.mcr1.marylandheights-mo.us.xo.net [216.156.1.89]
 16   317 ms   328 ms   335 ms  206.181.23.22
 17   258 ms   267 ms   257 ms  63-246-15-30.xiolink.com [63.246.15.30]
 18   250 ms   251 ms   248 ms  207.223.240.182

跟踪完成。
```

**只有深圳电信的服务器，都是星号(*)，这如何解释呢？**

## 6. tracepath测试

下面我们换一个命令tracepath，再测试一下。

左边是”深圳电信”的服务器，右边是”阿里云”的服务器。

![](http://blog.fens.me/wp-content/uploads/2013/06/net.png)

“no reply”, 这种情况。。。让老夫如何是好！！

本来是想看看，为什么连接bitbucket有问题，结果情况越来越复杂了。

## 6. 未解决的问题1 – 为什么显示星号(*)和no reply

网上查到一个合理的解释：“运营商，为了安全性，屏蔽了这些信息”。

我的疑问又来了，只是“深圳电信”才会做这个屏蔽，北京联通和阿里云都不做安全屏蔽。

## 7. 未解决的问题2 – bitbucket.org同步数据

我们还是用 “深圳电信”的服务器做实验。

![](http://blog.fens.me/wp-content/uploads/2013/06/net2.png)

同IP两台虚拟机，左边ping，右边git pull。

执行过程：

1. 右边 ping bitbucket.org，发现IP是207.223.240.181，停止
2. 左边 ping 207.223.240.181，一直畅通一直在动
3. 右边 执行git pull命令， 挂着不动了
4. 左边 ping 命令也挂着不动了
5. 两边同时都挂住了。。。我这泪啊！
6. 也不知道过几多长时间，10分钟，左边动了，右边出现“Already up-to-date.”同步成功。

**这次竟然成功了，很多时候等30分钟，都不会有动静的！！**

有没有同学能帮我解释一下原理及我所描述过程中的问题。由于网络知识的缺乏，只好写成文章求助大家了。

#### 转载请注明出处：http://blog.fens.me/vps-network-error/









