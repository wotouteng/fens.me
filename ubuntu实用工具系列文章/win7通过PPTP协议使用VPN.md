win7通过PPTP协议使用VPN
==============

[ubuntu实用工具系列文章](http://blog.fens.me/series-ubuntu/)，将介绍基于Linux ubuntu的各种工具软件的配置和使用。有些工具大家早已耳熟能详，有些工具经常用到但确依然陌生。我将记录我在使用操作系统时，安装及配置工具上面的一些方法，把使用心得记录下来也便于自己的以后查找和回忆。

#### 关于作者
+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com



#### 前言

GFW给大家的生活事带来了各种不便，只有靠我们自己才能改变这种不便。像facebook, twitter, youtube, google+, bitbucket, slideside…. 这些世界知名的网站，我们都没有办法直接访问，有时候你很难联想到为什么要屏蔽这些网站。特别是纯技术社区！

不过我们也有很多的技术，可以穿越这条封锁线。今天我们聊一下，win7通过PPTP协议使用VPN(Virtual Private Network)，如何看到外面的世界。

关于PPTP的服务器端配置：请参考 [在Ubuntu上安装PPTP VPN服务 ](http://blog.fens.me/ubuntu-vpn-pptp/)文章
  Ubuntu通过PPTP协议使用VPN：请参考 [Ubuntu通过PPTP协议使用VPN](http://blog.fens.me/vpn-pptp-client-ubuntu/) 文章

#### 目录

1. 获得VPN账号
2. win7客户端连接操作步骤
3. 看看外面的世界youtube

## 1. 获得VPN账号

当你有了基于PPTP协议的VPN账号后，怎么在win7中配置！

VPN账号包括3个部分：IP，用户名，密码

*比如：(只为演示说明，此账号不可用)*

+ ip: 123.123.123.123
+ 用户名：xx1
+ 密码: xx2

## 2. win7客户端连接操作步骤

通过win7自带的客户端软件进行连接。

### 流程图说明操作步骤：

![](http://blog.fens.me/wp-content/uploads/2013/07/vpn-pptp-process.png)

### 每一步操作细节

![](http://blog.fens.me/wp-content/uploads/2013/07/vpn-p1.png)

1. 开始–》控制面板–》网络和共享中心–》
2. 设置新的连接或者网络–》
3. 连接到工作区–》下一步–》
4. 创建新连接–》使用我的Internet连接（VPN）

![](http://blog.fens.me/wp-content/uploads/2013/07/win7-vpn-p2.png)

5. 键入要连接的Internet地址–》下一步

  输入内容：
  输入Internet地址： 123.123.123.123
  目录名称：VPN连接

6. 键入您的用户和密码–》创建

  输入内容：
  用户名: xx1
  密码: xx2

7. 点击右下角的网络标志：选择刚才新建好的 “VPN连接” –》右键–》属性
8. 安全–》VPN类型–》点对点隧道协议(PPTP)–》确定

![](http://blog.fens.me/wp-content/uploads/2013/07/win7-vpn-p3.png)

9. 点击右下角的网络标志：选择刚才新建好的 “VPN连接” –》连接
10. 选择刚才新建好的 “VPN连接” –》连接
11. 连接成功。

## 3. 看看外面的世界youtube

![](http://blog.fens.me/wp-content/uploads/2013/07/vpn10.png)

技术性文章，帮助大家更好的了解互联网。

#### 转载请注明出处：http://blog.fens.me/vpn-pptp-client-win7/


