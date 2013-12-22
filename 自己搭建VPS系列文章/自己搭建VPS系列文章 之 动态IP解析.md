自己搭建VPS系列文章 之 动态IP解析
==============

[自己搭建VPS系列文章](http://blog.fens.me/series-vps/)，介绍了如何利用自己的计算机资源，通过虚拟化技术搭建VPS。

在互联网2.0时代，每个人都有自己的博客，还有很多专属于自己的互联网应用。这些应用大部分都是互联网公司提供的。对于一些有能力的开发人员(geek)来说，他们希望做一些自己的应用，可以用到最新最炫的技术，并且有自己的域名，有自己的服务器。这时就要去租一些互联网上的VPS主机。VPS主机就相当于是一台远程的计算机，可以部署自己的应用程序，然后申请一个域名，就可以正式发布在互联网上了。本站“[@晒粉丝](http://www.fens.me/)” 就使用的[Linode主机](http://www.linode.com/?r=70e684fdc20f965f06b7b6e80f6acfd25db211ec)VPS在美国达拉斯机房。

其实，VPS还可以自己搭建的。只要我们有一台高性能的服务器，一个IP地址，一个路由。可以把一台高性能的服务器，很快的变成5台，10台，20台的虚拟VPS。我们就可以在自己的VPS上面的，发布各种的应用，还可以把剩余的服务器资源租给其他的互联网使用者。 本系列文章将分为以下几个部分介绍：“虚拟化技术选型”，[“动态IP解析”](http://blog.fens.me/vps-ip-dns)，[“在Ubuntu上安装KVM并搭建虚拟环境”](http://blog.fens.me/vps-kvm/)，[“给KVM虚拟机增加硬盘”](http://blog.fens.me/vps-kvm-disk)，[“Nova安装攻略”](http://blog.fens.me/vps-nova-setup)，“VPS内网的网络架构设计”，“VPS租用云服务”。

#### 关于作者：

+ 张丹(Conan), 程序员Java,R,PHP,Javacript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/vps-ip-dns/

![自己搭建VPS系列文章 之 动态IP解析](http://blog.fens.me/wp-content/uploads/2013/06/vps-ip-dns.png)

#### 文章目录：

1. 什么时候会用到动态IP解析
2. 环境准备
3. 实现原理
4. 动手实践

## 1 什么时候会用到动态IP解析？

家用PC通过PPPoE（宽带连接）拨号上网，如果一天二天没有关机，我们会神奇的发现IP变了。通过PPPoE获得的IP并不是固定的，每隔一段时间IP会被重新分配一次。

创业初期租不起外面的服务器，所以只好把服务器放在家中，面向互联网用户提供服务。但如果IP天天变，我们总不能天天盯着，发现变了人工去修改对应用的域名重新绑定新IP。这个时候，就会想到动态IP解析技术了。这个领域最有名的程序就是“花生壳”。其实这个技术实现起来并不复杂，接下来我们自己开发一套动态IP解析的程序。

## 2. 环境准备：

+ 1台内网服务器A (拨号上网) (xx.xx.xx.xx)，linux(cron, bash, curl)
+ 1台固定IP的外网服务器B(173.255.193.179) :  linux(bind9, PHP)
+ 1个域名：wtmart.com

![](http://blog.fens.me/wp-content/uploads/2013/06/%E5%8A%A8%E6%80%81IP%E8%A7%A3%E6%9E%90.jpg)

## 3. 实现原理：

1. 把域名绑定到服务器B： ip.wtmart.com ==> 173.255.193.179
2. 在服务器B创建一个web应用收集请求，并写入本地文件
3. 服务器A每分钟向服务器B发送请求，告诉服务器B，服务器A的IP是什么。
4. 服务器B收到A的IP，发现变化写入本地文件存储，并更新本地域名服务器(bind9)，用新IP指向lin.wtmart.com
5. 客户端机器C，手动选择DNS服务设置173.255.193.179。
6. 客户端机器C，SSH远程登陆服务器A，ssh root@lin.wtmart.com

## 下面我们一步一步实践一下：

### 1). 把域名绑定到服务器B： ip.wtmart.com ==> 173.255.193.179

wtmart.com的域名是从godaddy.com上面购买的，通过[dnspod](http://www.dnspod.cn/)做域名解析，难得的好服务。

![](http://blog.fens.me/wp-content/uploads/2013/06/wtmart.png)

注： 这里我们只需要关注ip.wtmart.com 其他的设置不用关心。

### 2). 在服务器B创建一个web应用收集请求，并写入本地文件

创建PHP程序

```{bash}

~ /deploy/dyip# pwd
/root/deploy/dyip
~ /deploy/dyip# ls -l
-rw-r--r-- 1 root root 38 Jun 2 13:40 getip.php
-rw-r--r-- 1 root root 13 Jun 12 03:52 ip
-rw-r--r-- 1 root root 807 Jun 2 15:52 sendip.php
```

### 接收IP的PHP脚本，sendip.php

```{bash}
~ vi sendip.php

<?php
#http://lin.wtmart.com/sendip.php?pwd=xxx
$content = file_get_contents('ip');
if ($_GET["pwd"] == 'xxx'){//简单认证
$ip = $_SERVER["REMOTE_ADDR"];
if ($content != $ip) {
$file = fopen('ip', 'w');
$result = fwrite($file, $ip);
fclose($file);

if ($fwrite === false) {
echo "update error!<br/>";
} else {
echo "Change to " . $ip;
echo "<br/>Write to DNS server";
}
} else {
echo "No change " . $ip;
}

} else {
echo "Sorry,you don't have permission<br/>";
echo "Server ip: " . $content;
}
```

### 简单认证是防止其他人，访问时IP被修改。

```{bash}

~ vi getip.php
<?php
echo file_get_contents('ip');
IP写入到本地文件,ip


~ vi ip
116.24.133.86
```

现在116.24.133.86就是服务器A，所用的IP地址，这个地址每天都会改变。

*配置PHP环境，我在这里就跳过了，也可以使用其他语言的程序。*

### 3). 服务器A每分钟向服务器B发送请求，告诉服务器B，服务器A的IP是什么 

在服务器A，设置cron定时器。

```{bash}
*/1 * * * * curl http://ip.wtmart.com/sendip.php?pwd=xxx
```

通过curl实现每分钟，发送一次请求。

### 4). 服务器B收到A的IP，发现变化写入本地文件存储，并更新本地域名服务器(bind9)，用新IP指向lin.wtmart.com

刚才的 sendip.php的脚本已经实现了，写入本地文件存储，接下来我们改一下sendip.php实现配置本地域名服务器(bind9)

修改sendip.php，增加rebind方法

```{bash}

~ vi sendip.php

<?php
#http://lin.wtmart.com/sendip.php?pwd=xxx
$content = file_get_contents('ip');
if ($_GET["pwd"] == 'xxx'){//简单认证
$ip = $_SERVER["REMOTE_ADDR"];
if ($content != $ip) {
$file = fopen('ip', 'w');
$result = fwrite($file, $ip);
fclose($file);

if ($fwrite === false) {
echo "update error!<br/>";
} else {
echo "Change to " . $ip;
echo "<br/>Write to DNS server";
rebind($ip);
}
} else {
echo "No change " . $ip;
}

} else {
echo "Sorry,you don't have permission<br/>";
echo "Server ip: " . $content;
}

# lin 在最后一行
function rebind($ip){
$f = "/etc/bind/db.wtmart.com";
$str=file_get_contents($f);
$a=preg_replace('/\\nlin(.+?)\\n/',"\nlin IN A ".$ip."\n",$str);
echo $a;
file_put_contents($f,$a);
exec('/etc/init.d/bind9 restart');
}
```

安装并配置域名服务器bind9

```{bash}

~ sudo apt-get install bind9

~ cd /etc/bind

# 打开域名转身的配置
~ vi named.conf.options
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

# 增加ip域名指向本地IP（服务器B）,lin的域名指向服务器A的IP
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
```

注： lin的域名，必须写到最后一行，并且上下都有空行。sendip.php中rebind方法。

重启bind9域名服务器

```{bash}

sudo /etc/init.d/bind9 restart
```

### 5). 客户端机器C，手动选择DNS服务设置173.255.193.179
linux配置DNS

![](http://blog.fens.me/wp-content/uploads/2013/06/linux-dns.png)

win7配置DNS

![](http://blog.fens.me/wp-content/uploads/2013/06/win-dns.png)

### 6). 客户端机器C，SSH远程登陆服务器A，ssh root@lin.wtmart.com
win7中命令行登陆

![](http://blog.fens.me/wp-content/uploads/2013/06/win-ssh.png)

我们完成了 动态IP解析。 通过域名服务器(bind9)，我们把动态IP和域名进行绑定。

另外，可能还有一些更简单的需求，比如服务器A的web工程，像这个功能我们在服务器B，配置一个nginx反向代理就可以实现了。

对于创业初期服务器放在家里的同学，希望对你们有用。

#### 转载请注明出处：http://blog.fens.me/vps-ip-dns
