Nova安装攻略
============

[自己搭建VPS系列文章](http://blog.fens.me/series-vps/)，介绍了如何利用自己的计算机资源，通过虚拟化技术搭建VPS。

在互联网2.0时代，每个人都有自己的博客，还有很多专属于自己的互联网应用。这些应用大部分都是互联网公司提供的。对于一些有能力的开发人员(geek)来说，他们希望做一些自己的应用，可以用到最新最炫的技术，并且有自己的域名，有自己的服务器。这时就要去租一些互联网上的VPS主机。VPS主机就相当于是一台远程的计算机，可以部署自己的应用程序，然后申请一个域名，就可以正式发布在互联网上了。本站“[@晒粉丝](http://www.fens.me/)” 就使用的[Linode主机](http://www.linode.com/?r=70e684fdc20f965f06b7b6e80f6acfd25db211ec)VPS在美国达拉斯机房。

其实，VPS还可以自己搭建的。只要我们有一台高性能的服务器，一个IP地址，一个路由。可以把一台高性能的服务器，很快的变成5台，10台，20台的虚拟VPS。我们就可以在自己的VPS上面的，发布各种的应用，还可以把剩余的服务器资源租给其他的互联网使用者。 本系列文章将分为以下几个部分介绍：“虚拟化技术选型”，[“动态IP解析”](http://blog.fens.me/vps-ip-dns)，[“在Ubuntu上安装KVM并搭建虚拟环境”](http://blog.fens.me/vps-kvm/)，[“给KVM虚拟机增加硬盘”](http://blog.fens.me/vps-kvm-disk)，[“Nova安装攻略”](http://blog.fens.me/vps-nova-setup)，“VPS内网的网络架构设计”，“VPS租用云服务”。

#### 关于作者：

+ 张丹(Conan), 程序员Java,R,PHP,Javacript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/vps-kvm/

![Nova安装攻略](http://blog.fens.me/wp-content/uploads/2013/07/openstack.png)

#### 前言

Nova是Openstack一个重要的组成部分，最核心的功能就是对虚拟机进行管理（kvm, qemu, xen, vmware, virtual box）。

本次安装实验对Linux Ubuntu系统版本有严格的要求，必须是12.04 LTS。其他版本模拟实验均不成功，请大家严格执行。

#### 本次实验的参考图书：
OpenStack Cloud Computing Cookbook
Chapter 1: Starting OpenStack Compute

#### 目录

1. nova安装方案
2. VirtrulBox虚拟机环境
3. 操作系统环境
4. 软件包依赖
5. nova配置
6. 创建nova实例
7. 登陆云实例
8. 错误总结

## 1. nova安装方案

使用VirtrulBox虚拟机，嵌套qemu虚拟机。nova安装在VirtrulBox环境中，云实例则安装qemu环境中。通过nova管理qemu云实例。

## 2. VirtrulBox虚拟机环境

**VirtrulBox虚拟机：** 6G内存，4核CPU, Linux Ubuntu 12.04 LTS
CPU支持VT-x/AMD-V,嵌套分页,PAE/NX

![](http://blog.fens.me/wp-content/uploads/2013/07/nova1.png)

### 3张虚拟网卡：

+ 网卡1：桥接网卡
+ 网卡2：Host-Only
+ 网卡3：Host-Only

![](http://blog.fens.me/wp-content/uploads/2013/07/vbox2.png)

Host-Only网卡在虚拟机中的全局设置：

![](http://blog.fens.me/wp-content/uploads/2013/07/vbox1.png)

## 3. 操作系统环境

### 再次强调：本次实验的Ubuntu版本，必须12.04 LTS

```{bash}

~ uname -a
Linux nova 3.2.0-23-generic #36-Ubuntu SMP Tue Apr 10 20:39:51 UTC 2012 x86_64 x86_64 x86_64 GNU/Linux

~ cat /etc/issue
Ubuntu 12.04 LTS \n \l

~ ifconfig
eth0      Link encap:Ethernet  HWaddr 08:00:27:90:e8:19
          inet addr:192.168.1.200  Bcast:192.168.1.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fe90:e819/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:162 errors:0 dropped:0 overruns:0 frame:0
          TX packets:132 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:16399 (16.3 KB)  TX bytes:22792 (22.7 KB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:16436  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```

### 创建openstack用户
创建新用户openstack，密码openstack，增加到sudo组

```{bash}

~ sudo useradd openstack
~ sudo passwd openstack  
~ sudo adduser openstack sudo

~ sudo mkdir -p /home/openstack
~ sudo chown openstack:openstack /home/openstack

~ ls -l /home
drwxr-xr-x 8 conan     conan     4096 Jul 13 17:07 conan
drwxr-xr-x 2 openstack openstack 4096 Jul 13 17:21 openstack
```

### 用openstack账号重新登陆，测试sudo命令
**以下所有操作请使用openstack用户**

```{bash}
ssh openstack@192.168.1.200

openstack@u1:~$ whoami
openstack

openstack@u1:~$ sudo -i  
[sudo] password for openstack:

root@u1:~# whoami
root

root@u1:~# exit
logout
```

### 虚拟机网卡配置

```{bash}
~ sudo vi /etc/network/interfaces

auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.1.200
netmask 255.255.255.0
network 192.168.1.0
broadcase 192.168.1.255
gateway 192.168.1.1

#public interface
auto eth1
iface eth1 inet static
address 172.16.0.1
netmask 255.255.0.0
network 172.16.0.0
broadcase 172.16.255.255

#private interface
auto eth2
iface eth2 inet manual
up ifconfig eth2 up
```

### 重新启动网卡

```{bash}

~ sudo /etc/init.d/networking restart

 * Running /etc/init.d/networking restart is deprecated because it may not enable again some interfaces
 * Reconfiguring network interfaces...                                                 ssh stop/waiting
ssh start/running, process 2040
ssh stop/waiting
ssh start/running, process 2082
ssh stop/waiting
ssh start/running, process 2121
                                                                            [ OK ]
~ ifconfig
eth0      Link encap:Ethernet  HWaddr 08:00:27:90:e8:19
          inet addr:192.168.1.200  Bcast:192.168.1.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fe90:e819/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:3408 errors:0 dropped:0 overruns:0 frame:0
          TX packets:2244 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:3321759 (3.3 MB)  TX bytes:250703 (250.7 KB)

eth1      Link encap:Ethernet  HWaddr 08:00:27:4e:06:74
          inet addr:172.16.0.1  Bcast:172.16.255.255  Mask:255.255.0.0
          inet6 addr: fe80::a00:27ff:fe4e:674/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:18 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:1656 (1.6 KB)  TX bytes:468 (468.0 B)

eth2      Link encap:Ethernet  HWaddr 08:00:27:5a:b1:1f
          inet6 addr: fe80::a00:27ff:fe5a:b11f/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:33 errors:0 dropped:0 overruns:0 frame:0
          TX packets:5 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:3156 (3.1 KB)  TX bytes:378 (378.0 B)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:16436  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```

### DNS配置

```{bash}

~ vi /etc/resolv.conf 
nameserver 8.8.8.8

~ ping www.163.com
PING 163.xdwscache.glb0.lxdns.com (101.23.128.17) 56(84) bytes of data.
64 bytes from 101.23.128.17: icmp_req=1 ttl=53 time=20.3 ms
64 bytes from 101.23.128.17: icmp_req=2 ttl=53 time=18.5 ms
```

## 4. 软件包依赖

### 更新源

```{bash}

~ sudo vi /etc/apt/sources.list

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
```

### nova相关软件安装

```{bash}

~ sudo apt-get update
~ sudo apt-get -y install rabbitmq-server nova-api nova-objectstore nova-scheduler nova-network nova-compute nova-cert glance qemu unzip
~ sudo apt-get install pm-utils
```

注：如果没有安装pm-utils，libvirtd日志中会有错误

```{bash}
Cannot find 'pm-is-supported' in path: No such file or directory
```

### 查看系统进程

```{bash}

~ pstree
init─┬─acpid
     ├─atd
     ├─beam.smp─┬─cpu_sup
     │          ├─inet_gethost───inet_gethost
     │          └─38*[{beam.smp}]
     ├─cron
     ├─dbus-daemon
     ├─dhclient3
     ├─dnsmasq
     ├─epmd
     ├─5*[getty]
     ├─irqbalance
     ├─2*[iscsid]
     ├─libvirtd───10*[{libvirtd}]
     ├─login───bash
     ├─rsyslogd───3*[{rsyslogd}]
     ├─sshd───sshd───bash
     ├─sshd─┬─sshd───sshd───sh───bash───pstree
     │      └─sshd───sshd───sh───bash
     ├─su───glance-api
     ├─su───glance-registry
     ├─su───nova-api
     ├─su───nova-cert
     ├─su───nova-network
     ├─su───nova-objectstor
     ├─su───nova-scheduler
     ├─su───nova-compute
     ├─udevd───2*[udevd]
     ├─upstart-socket-
     ├─upstart-udev-br
     └─whoopsie───{whoopsie}
```

### 安装ntp时间同步服务

```{bash}

~ sudo apt-get -y install ntp

#修改配置文件
~ sudo vi /etc/ntp.conf

#Replace ntp.ubuntu.com with an NTP server on your network
server ntp.ubuntu.com
server 127.127.1.0
fudge 127.127.1.0 stratum 10

#重启ntp
~ sudo service ntp restart

~ ps -aux|grep ntp
ntp       6990  0.0  0.0  37696  2180 ?        Ss   19:50   0:00 /usr/sbin/ntpd -p /var/run/ntpd.pid -g -u 113:120

#查看系统当前时间
~ date
Sat Jul 13 19:50:12 CST 2013
```

### 安装MySQL

```{bash}

~ sudo apt-get install mysql-server

#配置允许其他计算访问
~ sudo sed -i 's/127.0.0.1/0.0.0.0/g' /etc/mysql/my.cnf
~ sudo service mysql restart

#创建nova数据库及配置nova用户
~ MYSQL_PASS=mysql
~ mysql -uroot -p$MYSQL_PASS -e 'CREATE DATABASE nova;'
~ mysql -uroot -p$MYSQL_PASS -e "GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'%'"
~ mysql -uroot -p$MYSQL_PASS -e "SET PASSWORD FOR 'nova'@'%' = PASSWORD('openstack');"
```

## 5. nova配置

### 修改nova.conf配置文件
可以去掉–verbose，只是为了打印更多的日志信息。

```{bash}

~ sudo vi /etc/nova/nova.conf

--dhcpbridge_flagfile=/etc/nova/nova.conf
--dhcpbridge=/usr/bin/nova-dhcpbridge
--logdir=/var/log/nova
--state_path=/var/lib/nova
--lock_path=/var/lock/nova
--force_dhcp_release
--iscsi_helper=tgtadm
--libvirt_use_virtio_for_bridges
--connection_type=libvirt
--root_helper=sudo nova-rootwrap
--verbose
--ec2_private_dns_show_ip
--sql_connection=mysql://nova:openstack@172.16.0.1/nova
--use_deprecated_auth
--s3_host=172.16.0.1
--rabbit_host=172.16.0.1
--ec2_host=172.16.0.1
--ec2_dmz_host=172.16.0.1
--public_interface=eth1
--image_service=nova.image.glance.GlanceImageService
--glance_api_servers=172.16.0.1:9292
--auto_assign_floating_ip=true
--scheduler_default_filters=AllHostsFilter
```

### 修改VMM设置，nova-compute.conf
这里要使用qemu虚拟机，如果我们不是嵌套虚拟化的模式，建议使用kvm虚拟机。

```{bash}

~ sudo vi /etc/nova/nova-compute.conf

--libvirt_type=qemu
```

### 把nova源数据写入MySQL

```{bash}

~ sudo nova-manage db sync

2013-07-14 21:18:56 DEBUG nova.utils [-] backend  from (pid=8750) __get_backend /usr/lib/python2.7/dist-packages/nova/utils.py:663
2013-07-14 21:19:06 WARNING nova.utils [-] /usr/lib/python2.7/dist-packages/sqlalchemy/pool.py:639: SADeprecationWarning: The 'listeners' argument to Pool (and create_engine()) is deprecated.  Use event.listen().
  Pool.__init__(self, creator, **kw)

2013-07-14 21:19:06 WARNING nova.utils [-] /usr/lib/python2.7/dist-packages/sqlalchemy/pool.py:145: SADeprecationWarning: Pool.add_listener is deprecated.  Use event.listen()
  self.add_listener(l)

2013-07-14 21:19:06 AUDIT nova.db.sqlalchemy.fix_dns_domains [-] Applying database fix for Essex dns_domains table.
```

### 创建openstack私有网络

```{bash}

~ sudo nova-manage network create vmnet --fixed_range_v4=10.0.0.0/8 --network_size=64 --bridge_interface=eth2

2013-07-14 21:19:34 DEBUG nova.utils [req-152fee41-ddc9-4ac5-902d-fb93f7af67a8 None None] backend  from (pid=8807) __get_backend /usr/lib/python2.7/dist-packages/nova/utils.py:663
```

### 设置openstack浮动IP

```{bash}
~ sudo nova-manage floating create --ip_range=172.16.1.0/24

2013-07-14 21:19:48 DEBUG nova.utils [req-7171e8bc-6542-40d2-b24c-b4593505fd87 None None] backend  from (pid=8814) __get_backend /usr/lib/python2.7/dist-packages/nova/utils.py:663
```

### 查看mysql中nova数据库

```{bash}

~ mysql -uroot -p

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| ape_biz            |
| mysql              |
| nova               |
| performance_schema |
| test               |
+--------------------+
6 rows in set (0.00 sec)

mysql> use nova

mysql> show tables;
+-------------------------------------+
| Tables_in_nova                      |
+-------------------------------------+
| agent_builds                        |
| aggregate_hosts                     |
| aggregate_metadata                  |
| aggregates                          |
| auth_tokens                         |
| block_device_mapping                |
| bw_usage_cache                      |
| cells                               |
| certificates                        |
| compute_nodes                       |
| console_pools                       |
| consoles                            |
| dns_domains                         |
| fixed_ips                           |
| floating_ips                        |
| instance_actions                    |
| instance_faults                     |
| instance_info_caches                |
| instance_metadata                   |
| instance_type_extra_specs           |
| instance_types                      |
| instances                           |
| iscsi_targets                       |
| key_pairs                           |
| migrate_version                     |
| migrations                          |
| networks                            |
| projects                            |
| provider_fw_rules                   |
| quotas                              |
| s3_images                           |
| security_group_instance_association |
| security_group_rules                |
| security_groups                     |
| services                            |
| sm_backend_config                   |
| sm_flavors                          |
| sm_volume                           |
| snapshots                           |
| user_project_association            |
| user_project_role_association       |
| user_role_association               |
| users                               |
| virtual_interfaces                  |
| virtual_storage_arrays              |
| volume_metadata                     |
| volume_type_extra_specs             |
| volume_types                        |
| volumes                             |
+-------------------------------------+
49 rows in set (0.00 sec)
重启服务nova,libvirt,glance


#停止
~ sudo stop nova-compute
~ sudo stop nova-network
~ sudo stop nova-api
~ sudo stop nova-scheduler
~ sudo stop nova-objectstore
~ sudo stop nova-cert

~ sudo stop libvirt-bin
~ sudo stop glance-registry
~ sudo stop glance-api

#启动
~ sudo start nova-compute
~ sudo start nova-network
~ sudo start nova-api
~ sudo start nova-scheduler
~ sudo start nova-objectstore
~ sudo start nova-cert

~ sudo start libvirt-bin
~ sudo start glance-registry
~ sudo start glance-api

#查看系统进程树
~ pstree
init─┬─acpid
     ├─atd
     ├─beam.smp─┬─cpu_sup
     │          ├─inet_gethost───inet_gethost
     │          └─38*[{beam.smp}]
     ├─cron
     ├─dbus-daemon
     ├─dhclient3
     ├─dnsmasq
     ├─epmd
     ├─5*[getty]
     ├─irqbalance
     ├─2*[iscsid]
     ├─libvirtd───10*[{libvirtd}]
     ├─login───bash
     ├─mysqld───19*[{mysqld}]
     ├─ntpd
     ├─rsyslogd───3*[{rsyslogd}]
     ├─sshd───sshd───bash
     ├─sshd─┬─sshd───sshd───sh───bash───pstree
     │      └─sshd───sshd───sh───bash
     ├─su───glance-registry
     ├─su───glance-api
     ├─su───nova-network
     ├─su───nova-api
     ├─su───nova-scheduler
     ├─su───nova-objectstor
     ├─su───nova-cert
     ├─su───nova-compute
     ├─udevd───2*[udevd]
     ├─upstart-socket-
     ├─upstart-udev-br
     └─whoopsie───{whoopsie}
创建nova用户，角色，项目


#创建用户
~ sudo nova-manage user admin openstack
2013-07-14 21:22:00 DEBUG nova.utils [req-6a95dd03-04db-4f60-9198-d77a4d4936e8 None None] backend  from (pid=9254) __get_backend /usr/lib/python2.7/dist-packages/nova/utils.py:663
2013-07-14 21:22:00 AUDIT nova.auth.manager [-] Created user openstack (admin: True)
export EC2_ACCESS_KEY=62ff82fa-74a9-4ffb-a420-ea190e893863
export EC2_SECRET_KEY=f1f32aed-85fe-406d-8f28-bbf02d7a7134

#创建角色
~ sudo nova-manage role add openstack cloudadmin
2013-07-14 21:22:15 AUDIT nova.auth.manager [-] Adding sitewide role cloudadmin to user openstack
2013-07-14 21:22:15 DEBUG nova.utils [req-a9d8cdfa-263c-4d6a-8c69-d6571aabee00 None None] backend  from (pid=9262) __get_backend /usr/lib/python2.7/dist-packages/nova/utils.py:663

#创建项目
~ sudo nova-manage project create cookbook openstack
2013-07-14 21:22:34 DEBUG nova.utils [req-3a340500-6674-439e-ac95-e28954637cf5 None None] backend  from (pid=9395) __get_backend /usr/lib/python2.7/dist-packages/nova/utils.py:663
2013-07-14 21:22:34 AUDIT nova.auth.manager [-] Created project cookbook with manager openstack

~ sudo nova-manage project zipfile cookbook openstack
2013-07-14 21:22:49 DEBUG nova.utils [req-429e7839-6009-4862-98c5-af01ceac9cee None None] backend  from (pid=9402) __get_backend /usr/lib/python2.7/dist-packages/nova/utils.py:663
2013-07-14 21:22:49 DEBUG nova.utils [-] Running cmd (subprocess): openssl genrsa -out /tmp/tmprZNpYT/temp.key 1024 from (pid=9402) execute /usr/lib/python2.7/dist-packages/nova/utils.py:224
2013-07-14 21:22:49 DEBUG nova.utils [-] Running cmd (subprocess): openssl req -new -key /tmp/tmprZNpYT/temp.key -out /tmp/tmprZNpYT/temp.csr -batch -subj /C=US/ST=California/O=OpenStack/OU=NovaDev/CN=cookbook-openstack-2013-07-14T13:22:49Z from (pid=9402) execute /usr/lib/python2.7/dist-packages/nova/utils.py:224
2013-07-14 21:22:49 DEBUG nova.crypto [-] Flags path: /var/lib/nova/CA from (pid=9402) _sign_csr /usr/lib/python2.7/dist-packages/nova/crypto.py:290
2013-07-14 21:22:49 DEBUG nova.utils [-] Running cmd (subprocess): openssl ca -batch -out /tmp/tmpJZvmrM/outbound.csr -config ./openssl.cnf -infiles /tmp/tmpJZvmrM/inbound.csr from (pid=9402) execute /usr/lib/python2.7/dist-packages/nova/utils.py:224
2013-07-14 21:22:49 DEBUG nova.utils [-] Running cmd (subprocess): openssl x509 -in /tmp/tmpJZvmrM/outbound.csr -serial -noout from (pid=9402) execute /usr/lib/python2.7/dist-packages/nova/utils.py:224
2013-07-14 21:22:49 WARNING nova.auth.manager [-] No vpn data for project cookbook
```

### 安装命令行工具及配置

```{bash}

~ sudo apt-get install euca2ools python-novaclient unzip

~ pwd
/home/openstack

~ ls -l
-rw-r--r-- 1 root root 5930 Jul 13 20:38 nova.zip

#解压工具包
~ unzip nova.zip
Archive:  nova.zip
extracting: novarc
extracting: pk.pem
extracting: cert.pem
extracting: cacert.pem

#增加环境变量
~ . novarc

#查看环境变量
~ env
LC_PAPER=en_US
LC_ADDRESS=en_US
LC_MONETARY=en_US
SHELL=/bin/sh
TERM=xterm
SSH_CLIENT=192.168.1.11 60377 22
LC_NUMERIC=en_US
EUCALYPTUS_CERT=/home/openstack/cacert.pem
OLDPWD=/var/log/libvirt
SSH_TTY=/dev/pts/0
LC_ALL=en_US.UTF-8
USER=openstack
LC_TELEPHONE=en_US
NOVA_CERT=/home/openstack/cacert.pem
EC2_SECRET_KEY=6f964a16-6036-44ef-bdf3-23dff94f5b94
NOVA_PROJECT_ID=cookbook
EC2_USER_ID=42
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/home/conan/toolkit/jdk16/bin:/home/conan/toolkit/cassandra/bin
MAIL=/var/mail/openstack
NOVA_VERSION=1.1
LC_IDENTIFICATION=en_US
NOVA_USERNAME=openstack
PWD=/home/openstack
JAVA_HOME=/home/conan/toolkit/jdk16
LANG=en_US.UTF-8
CASSANDRA_HOME=/home/conan/toolkit/cassandra125
LC_MEASUREMENT=en_US
NOVA_API_KEY=openstack
NOVA_URL=http://172.16.0.1:8774/v1.1/
SHLVL=1
HOME=/home/openstack
LANGUAGE=en_US:en
EC2_URL=http://172.16.0.1:8773/services/Cloud
LOGNAME=openstack
SSH_CONNECTION=192.168.1.11 60377 192.168.1.200 22
EC2_ACCESS_KEY=openstack:cookbook
EC2_PRIVATE_KEY=/home/openstack/pk.pem
DISPLAY=localhost:10.0
S3_URL=http://172.16.0.1:3333
LC_TIME=en_US
EC2_CERT=/home/openstack/cert.pem
LC_NAME=en_US
_=/usr/bin/env

#创建密钥
~ euca-add-keypair openstack > openstack.pem
~ chmod 0600 *.pem

~ ls -l
-rw------- 1 openstack openstack 1029 Jul 13 20:38 cacert.pem
-rw------- 1 openstack openstack 2515 Jul 13 20:38 cert.pem
-rw------- 1 openstack openstack 1113 Jul 13 20:38 novarc
-rw-r--r-- 1 root      root      5930 Jul 13 20:38 nova.zip
-rw------- 1 openstack openstack  954 Jul 13 20:50 openstack.pem
-rw------- 1 openstack openstack  887 Jul 13 20:38 pk.pem
```

### 查看nova服务

```{bash}
~ euca-describe-availability-zones verbose
AVAILABILITYZONE        nova    available
AVAILABILITYZONE        |- nova
AVAILABILITYZONE        | |- nova-scheduler     enabled :-) 2013-07-14 13:23:53
AVAILABILITYZONE        | |- nova-compute       enabled :-) 2013-07-14 13:23:53
AVAILABILITYZONE        | |- nova-cert  enabled :-) 2013-07-14 13:23:53
AVAILABILITYZONE        | |- nova-network       enabled :-) 2013-07-14 13:23:53
```

## 6. 创建nova实例

### 上传云实例到虚拟主机
ubuntu-12.04-server-cloudimg-i386.tar.gz文件，请自己下载：http://uec-images.ubuntu.com/releases/precise/release/ubuntu-12.04-server-cloudimg-i386.tar.gz

```{bash}

~ scp ubuntu-12.04-server-cloudimg-i386.tar.gz openstack@192.168.1.200:/home/openstack
ubuntu-12.04-server-cloudimg-i386.tar.gz                                              100%  206MB -11880.-5KB/s   00:07

~ ls -l
-rw------- 1 openstack openstack      1029 Jul 14 21:22 cacert.pem
-rw------- 1 openstack openstack      2515 Jul 14 21:22 cert.pem
-rw------- 1 openstack openstack      1113 Jul 14 21:22 novarc
-rw-r--r-- 1 root      root           5930 Jul 14 21:22 nova.zip
-rw------- 1 openstack openstack       954 Jul 14 21:23 openstack.pem
-rw------- 1 openstack openstack       887 Jul 14 21:22 pk.pem
-rw-r--r-- 1 openstack openstack 215487341 Jul 14 21:25 ubuntu-12.04-server-cloudimg-i386.tar.gz
```

### 安装云实例

```{bash}

~ cloud-publish-tarball ubuntu-12.04-server-cloudimg-i386.tar.gz images i386

Sun Jul 14 21:25:34 CST 2013: ====== extracting image ======
Warning: no ramdisk found, assuming '--ramdisk none'
kernel : precise-server-cloudimg-i386-vmlinuz-virtual
ramdisk: none
image  : precise-server-cloudimg-i386.img
Sun Jul 14 21:25:40 CST 2013: ====== bundle/upload kernel ======
Sun Jul 14 21:26:06 CST 2013: ====== bundle/upload image ======
Sun Jul 14 21:27:04 CST 2013: ====== done ======
emi="ami-00000002"; eri="none"; eki="aki-00000001";
```

### 查看云实例
两种查看方式：euca客户端和nova客户端

注：注册image的操作要经过：decrypting,untarring,uploading，available这几个状态，需要等待几分钟

```{bash}
~ euca-describe-images

IMAGE   aki-00000001    images/precise-server-cloudimg-i386-vmlinuz-virtual.manifest.xmlavailable        private         i386    kernel                          instance-store
IMAGE   ami-00000002    images/precise-server-cloudimg-i386.img.manifest.xml            available        private         i386    machine aki-00000001                    instance-store

~ nova image-list
+--------------------------------------+-----------------------------------------------------+--------+--------+
|                  ID                  |                         Name                        | Status | Server |
+--------------------------------------+-----------------------------------------------------+--------+--------+
| 306eb471-bbc5-495e-b7a1-484e11f71502 | images/precise-server-cloudimg-i386-vmlinuz-virtual | ACTIVE |        |
| 9dbf632e-b0d8-4230-a0e5-ee3836040492 | images/precise-server-cloudimg-i386.img             | ACTIVE |        |
+--------------------------------------+-----------------------------------------------------+--------+--------+
```

### 设置云实例网络

```{bash}

~ euca-authorize default -P tcp -p 22 -s 0.0.0.0/0
GROUP   default
PERMISSION      default ALLOWS  tcp     22      22      FROM    CIDR    0.0.0.0/0

~ euca-authorize default -P icmp -t -1:-1
GROUP   default
PERMISSION      default ALLOWS  icmp    -1      -1      FROM    CIDR    0.0.0.0/0
```

### 查看系统空间

```{bash}

~ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        34G  3.7G   29G  12% /
udev            3.0G  4.0K  3.0G   1% /dev
tmpfs           1.2G  316K  1.2G   1% /run
none            5.0M     0  5.0M   0% /run/lock
none            3.0G     0  3.0G   0% /run/shm
cgroup          3.0G     0  3.0G   0% /sys/fs/cgroup

~ nova flavor-list
+----+-----------+-----------+------+-----------+------+-------+-------------+
| ID |    Name   | Memory_MB | Disk | Ephemeral | Swap | VCPUs | RXTX_Factor |
+----+-----------+-----------+------+-----------+------+-------+-------------+
| 1  | m1.tiny   | 512       | 0    | 0         |      | 1     | 1.0         |
| 2  | m1.small  | 2048      | 10   | 20        |      | 1     | 1.0         |
| 3  | m1.medium | 4096      | 10   | 40        |      | 2     | 1.0         |
| 4  | m1.large  | 8192      | 10   | 80        |      | 4     | 1.0         |
| 5  | m1.xlarge | 16384     | 10   | 160       |      | 8     | 1.0         |
+----+-----------+-----------+------+-----------+------+-------+-------------+
```

还有29G硬盘空间，根据云实例创建favor-list，我可以选择 tiny或者small。

### 这里我选择用tiny模式

```{bash}
~ euca-run-instances ami-00000002 -t m1.tiny -k openstack
RESERVATION     r-f6y5tydu      cookbook        default
INSTANCE        i-00000001      ami-00000002                    pending openstack (cookbook, None)       0               m1.tiny 2013-07-14T13:30:02.000Z        unknown zone    aki-00000001                     monitoring-disabled     
```

### 查看云实例列表
这个操作要等几分钟：

```{bash}

~ euca-describe-instances
RESERVATION     r-f6y5tydu      cookbook        default
INSTANCE        i-00000001      ami-00000002    172.16.1.1      10.0.0.3        running openstack (cookbook, nova)       0               m1.tiny 2013-07-14T13:30:02.000Z        nova     aki-00000001                    monitoring-disabled     172.16.1.1      10.0.0.3instance-store

~ nova list
+--------------------------------------+----------+--------+----------------------------+
|                  ID                  |   Name   | Status |          Networks          |
+--------------------------------------+----------+--------+----------------------------+
| d6e5fe88-1950-48f4-853a-2fd57e6c72f4 | Server 1 | ACTIVE | vmnet=10.0.0.3, 172.16.1.1 |
+--------------------------------------+----------+--------+----------------------------+

~ top
11424 libvirt-  20   0 1451m 322m 7284 S  105  5.4   1:20.58 qemu-system-x86
 8962 nova      20   0  265m 104m 5936 S    3  1.8   0:17.57 nova-api
   35 root      25   5     0    0    0 S    1  0.0   0:00.66 ksmd
 5933 rabbitmq  20   0 2086m  28m 2468 S    1  0.5   0:03.33 beam.smp
 8969 nova      20   0  195m  48m 4748 S    1  0.8   0:02.77 nova-scheduler
 9190 nova      20   0 1642m  63m 6660 S    1  1.1   0:08.14 nova-compute
 8522 mysql     20   0 1118m  54m 8276 S    1  0.9   0:04.45 mysqld
 8951 nova      20   0  197m  50m 4756 S    1  0.9   0:03.09 nova-network
```

## 7. 登陆云实例

### ping通过云实例

```{bash}
~ ping 172.16.1.1
PING 172.16.1.1 (172.16.1.1) 56(84) bytes of data.
64 bytes from 172.16.1.1: icmp_req=1 ttl=64 time=5.98 ms
64 bytes from 172.16.1.1: icmp_req=2 ttl=64 time=2.00 ms
64 bytes from 172.16.1.1: icmp_req=3 ttl=64 time=3.27 ms
```

### 通过证书登陆云实例

```{bash}
~ ssh -i openstack.pem ubuntu@172.16.1.1
The authenticity of host '172.16.1.1 (172.16.1.1)' can't be established.
ECDSA key fingerprint is b8:0b:a6:18:0d:30:06:ea:79:c7:17:e5:29:34:55:39.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '172.16.1.1' (ECDSA) to the list of known hosts.
```

### 在云实例简单操作

```{bash}

~ ubuntu@server-1:~$ who
ubuntu   pts/0        2013-07-14 13:35 (172.16.1.1)

~ ubuntu@server-1:~$ ifconfig
eth0      Link encap:Ethernet  HWaddr fa:16:3e:22:75:8f
          inet addr:10.0.0.3  Bcast:10.0.0.63  Mask:255.255.255.192
          inet6 addr: fe80::f816:3eff:fe22:758f/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:263 errors:0 dropped:0 overruns:0 frame:0
          TX packets:250 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:29823 (29.8 KB)  TX bytes:28061 (28.0 KB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:16436  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```

nova实验安装完成！！

## 8. 错误总结

看上去上面过程，就是命令操作，但其实会遇到很多的问题。

1. 版本问题：如果用Ubunut 12.04.2时，和上面完全一样的操作，在创建云实例的时候会失败。云实例起不来，从RUNNING状态直接进入SHUTDOWN状态。ping不通，ssh也连不上。

2. 版本问题：如果用Ubunut 13.03时，命令操作,Nova数据库,Nova配置文件, Nova服务都已经和书中实例不一样了。按照书的操作无法进行。

3. 依赖软件问题：pm-utils这个软件竟然没有做libvirtd的依赖，需要手动安装。

如果没有安装会出现下面的错误

```{bash}

~ sudo cat /var/log/libvirt/libvirtd.log

2013-07-13 12:20:25.511+0000: 9292: info : libvirt version: 0.9.8
2013-07-13 12:20:25.511+0000: 9292: error : virExecWithHook:327 : Cannot find 'pm-is-supported' in path: No such file or directory
2013-07-13 12:20:25.511+0000: 9292: warning : qemuCapsInit:856 : Failed to get host power management capabilities
2013-07-13 12:20:25.653+0000: 9292: error : virExecWithHook:327 : Cannot find 'pm-is-supported' in path: No such file or directory
2013-07-13 12:20:25.653+0000: 9292: warning : lxcCapsInit:77 : Failed to get host power management capabilities
2013-07-13 12:20:25.654+0000: 9292: error : virExecWithHook:327 : Cannot find 'pm-is-supported' in path: No such file or directory
2013-07-13 12:20:25.655+0000: 9292: warning : umlCapsInit:87 : Failed to get host power management capabilities

#解决pm-is-supported错误
~ sudo apt-get -y install pm-utils
~ sudo stop libvirt-bin
~ sudo start libvirt-bin
```

4. nova状态问题：注册image时间，会卡在untarring永久不动了。nova image-list状态是saving。

这是因为image由于各种原因没有注册成功，我第一次就由于硬盘满了，就一直卡在这个状态了，也看不到错误信息太郁闷了。

解决办法:删除原来卡住的 image，重新注册。

```{bash}
euca-deregister aki-00000001
euca-deregister ami-00000002
```

耐心很重要，坚持就是胜利。

#### 转载请注明出处：http://blog.fens.me/vps-nova-setup/

























