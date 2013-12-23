Nodejs创建HTTPS服务器
===============

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-https-server/

![Nodejs创建HTTPS服务器](http://blog.fens.me/wp-content/uploads/2013/12/nodejs-https.png)

#### 前言

互联网应用越来越多，网络安全问题应该值得大家注意起来，特别是对于有在线支付业务的网站！为保证用户的账户安全，应该使用HTTPS协议代替HTTP协议，在数据传输层保持加密。

大部分的美国网站，当涉及到个人信息时，如果不提供HTTPS的服务，多数用户都会拒绝使用的。中国的网民什么时候，才会有这种意识呢？

#### 目录

1. HTTP和HTTPS介绍
2. 用Nodejs创建HTTPS服务器(Win7)
3. 用Nodejs创建HTTPS服务器(Linux)
4. 抓取请求，验证加密

## 1. HTTP和HTTPS介绍

HTTP: 超文本传输协议 (HTTP-Hypertext transfer protocol) 是一种详细规定了浏览器和万维网服务器之间互相通信的规则，通过因特网传送万维网文档的数据传送协议。

HTTPS:（全称：Hypertext Transfer Protocol over Secure Socket Layer），是以安全为目标的HTTP通道，简单讲是HTTP的安全版。即HTTP下加入SSL层，HTTPS的安全基础是SSL，因此加密的详细内容就需要SSL。 它是一个URI scheme（抽象标识符体系），句法类同http:体系。用于安全的HTTP数据传输。https:URL表明它使用了HTTP，但HTTPS存在不同于HTTP的默认端口及一个加密/身份验证层（在HTTP与TCP之间）。这个系统的最初研发由网景公司进行，提供了身份验证与加密通讯方法，现在它被广泛用于万维网上安全敏感的通讯，例如交易支付方面。

### HTTPS和HTTP的区别

* https协议需要到ca申请证书，一般免费证书很少，需要交费。
* http是超文本传输协议，信息是明文传输，https 则是具有安全性的ssl加密传输协议。
* http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。
* http的连接很简单，是无状态的；HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全。

HTTP和HTTPS的介绍，摘自http://baike.baidu.com/view/14121.htm

在Nodejs中，我们可以通过内置的https库，来实现HTTPS服务器。

Nodejs的HTTPS使用文档：http://nodejs.org/api/https.html

## 2. 用Nodejs创建HTTPS服务器(Win7)

### 系统环境

* win7 64bit
* IP: 192.168.1.13
* Nodejs:v0.10.5
* Npm:1.2.19

创建express的开发环境

```{bash}

~ D:\workspace\javascript>express -e  nodejs-https
~ D:\workspace\javascript>cd nodejs-https && npm install

ejs@0.8.5 node_modules\ejs
express@3.2.6 node_modules\express
├── methods@0.0.1
├── fresh@0.1.0
├── cookie-signature@1.0.1
├── range-parser@0.0.4
├── debug@0.7.4
├── buffer-crc32@0.2.1
├── cookie@0.1.0
├── commander@0.6.1
├── mkdirp@0.3.4
├── send@0.1.0 (mime@1.2.6)
└── connect@2.7.11 (pause@0.0.1, qs@0.6.5, bytes@0.2.0, cookie@0.0.5, formidable@1.0.14, send@0.1.1)
```

我本地已经安装了[git客户端msysgit](http://msysgit.github.io/)，其中包括了openssl程序。

```{bash}

~ D:\workspace\javascript\nodejs-https>git --version
git version 1.8.1.msysgit.1

~ D:\workspace\javascript\nodejs-https>openssl version -a
OpenSSL 0.9.8e 23 Feb 2007
built on: Sat Sep 15 20:34:58 EDT 2007
platform: MSys
options:  bn(64,32) md2(int) rc4(idx,int) des(ptr,risc1,16,long) blowfish(idx)
compiler: gcc -D_WINDLL -DOPENSSL_PIC -DOPENSSL_THREADS  -DDSO_DLFCN -DHAVE_DLFCN_H -DTERMIOS -DL_ENDIAN -D__CYGWIN__ -f
omit-frame-pointer -fnative-struct -O3 -mcpu=pentium -march=i486 -Wall -DOPENSSL_BN_ASM_PART_WORDS -DOPENSSL_IA32_SSE2 -
DSHA1_ASM -DMD5_ASM -DRMD160_ASM -DAES_ASM
OPENSSLDIR: "/usr/ssl"
```

openssl生成证书文件

```{bash}
#生成私钥key文件：
~ D:\workspace\javascript\nodejs-https>openssl genrsa -out privatekey.pem 1024
Generating RSA private key, 1024 bit long modulus
...........................++++++
........++++++
e is 65537 (0x10001)

#通过私钥生成CSR证书签名
~ D:\workspace\javascript\nodejs-https>openssl req -new -key privatekey.pem -out certrequest.csr
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:CN
State or Province Name (full name) [Some-State]:Beijing
Locality Name (eg, city) []:Beijing
Organization Name (eg, company) [Internet Widgits Pty Ltd]:fens.me
Organizational Unit Name (eg, section) []:fens.me
Common Name (eg, YOUR name) []:Conan Zhang
Email Address []:bsspirit@gmail.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

# 通过私钥和证书签名生成证书文件
~ D:\workspace\javascript\nodejs-https>openssl x509 -req -in certrequest.csr -signkey privatekey.pem -out certificate.pem
Signature ok
subject=/C=CN/ST=Beijing/L=Beijing/O=fens.me/OU=fens.me/CN=Conan Zhang/emailAddress=bsspirit@gmail.com
Getting Private key
```

新生成了3个文件：certificate.pem, certrequest.csr, privatekey.pem

```{bash}

~ D:\workspace\javascript\nodejs-https>ls -l
total 17
-rwx------  1 4294967295 mkpasswd 877 Dec 14 10:53 app.js
-rwx------  1 4294967295 mkpasswd 956 Dec 14 11:22 certificate.pem
-rwx------  1 4294967295 mkpasswd 704 Dec 14 11:21 certrequest.csr
drwx------+ 1 4294967295 mkpasswd   0 Dec 14 11:10 node_modules
-rwx------  1 4294967295 mkpasswd 216 Dec 14 11:03 package.json
-rwx------  1 4294967295 mkpasswd 887 Dec 14 11:20 privatekey.pem
drwx------+ 1 4294967295 mkpasswd   0 Dec 14 10:53 public
drwx------+ 1 4294967295 mkpasswd   0 Dec 14 10:53 routes
drwx------+ 1 4294967295 mkpasswd   0 Dec 14 10:53 views
```

* privatekey.pem: 私钥
* certrequest.csr: CSR证书签名
* certificate.pem: 证书文件

修改启动文件：app.js

```{bash}

~ vi app.js

//最下面
var https = require('https')
    ,fs = require("fs");

var options = {
    key: fs.readFileSync('./privatekey.pem'),
    cert: fs.readFileSync('./certificate.pem')
};

https.createServer(options, app).listen(3011, function () {
    console.log('Https server listening on port ' + 3011);
});
```

启动服务器：

```{bash}
~ D:\workspace\javascript\nodejs-https>node app.js

Express server listening on port 3000
Https server listening on port 3011
```

打开浏览器：HTTP访问

![](http://blog.fens.me/wp-content/uploads/2013/12/https-http.png)

HTTPS访问

![](http://blog.fens.me/wp-content/uploads/2013/12/https-https.png)

查看证书

![](http://blog.fens.me/wp-content/uploads/2013/12/https-https-cert.png)

由于我们证书是自己创建的，没有经过第三方机构的验证，因此会出现警告的提示。大家可以去有资质的网络运营商，去申请自己的证书。比如：godaddy SSL Certificates，优惠码：WOWfensme

这样我们就在win7完整的HTTPS服务器创建。

## 3. 用Nodejs创建HTTPS服务器(Linux)

创建过程与win7类似

### 系统环境

* Linux: Ubuntu 12.04.2 LTS 64bit deskop
* IP: 192.168.1.20
* Nodejs: npm 1.2.21
* node v0.11.2

创建express项目

```{bash}

~ cd /home/conan/nodejs
~ express -e  nodejs-https
~ cd nodejs-https
~ sudo npm install
```

用openssl生成证书文件

```{bash}
~ openssl version -a
OpenSSL 1.0.1 14 Mar 2012
built on: Tue Jun  4 07:26:06 UTC 2013
platform: debian-amd64
options:  bn(64,64) rc4(16x,int) des(idx,cisc,16,int) blowfish(idx)
compiler: cc -fPIC -DOPENSSL_PIC -DZLIB -DOPENSSL_THREADS -D_REENTRANT -DDSO_DLFCN -DHAVE_DLFCN_H -m64 -DL_ENDIAN -DTERMIO -g -O2 -fstack-protector --param=ssp-buffer-size=4 -Wformat -Wformat-security -Werror=format-security -D_FORTIFY_SOURCE=2 -Wl,-Bsymbolic-functions -Wl,-z,relro -Wa,--noexecstack -Wall -DOPENSSL_NO_TLS1_2_CLIENT -DOPENSSL_MAX_TLS1_2_CIPHER_LENGTH=50 -DMD32_REG_T=int -DOPENSSL_IA32_SSE2 -DOPENSSL_BN_ASM_MONT -DOPENSSL_BN_ASM_MONT5 -DOPENSSL_BN_ASM_GF2m -DSHA1_ASM -DSHA256_ASM -DSHA512_ASM -DMD5_ASM -DAES_ASM -DVPAES_ASM -DBSAES_ASM -DWHIRLPOOL_ASM -DGHASH_ASM
OPENSSLDIR: "/usr/lib/ssl"

~ openssl genrsa -out privatekey.pem 1024
~ openssl req -new -key privatekey.pem -out certrequest.csr 
~ openssl x509 -req -in certrequest.csr -signkey privatekey.pem -out certificate.pem
```

修改文件app.js

```{bash}

~ vi app.js

//在最下面
var https = require('https')
    ,fs = require("fs");

var options = {
    key: fs.readFileSync('./privatekey.pem'),
    cert: fs.readFileSync('./certificate.pem')
};

https.createServer(options, app).listen(3011, function () {
    console.log('Https server listening on port ' + 3011);
});
```

启动服务器

```{bash}
~ node app.js

Express server listening on port 3000
Https server listening on port 3011
```

打开浏览器：

![](http://blog.fens.me/wp-content/uploads/2013/12/https-https-linux.png)

## 4. 抓取请求，验证加密

为了验证数据在传输过程中是加密的，我们用wireshark抓取网络包。

### HTTP请求：http://192.168.1.20:3000/users?a=111

![](http://blog.fens.me/wp-content/uploads/2013/12/http-request.png)

### HTTPS请求：https://192.168.1.20:3011/users?a=112

![](http://blog.fens.me/wp-content/uploads/2013/12/https-request.png)

在HTTP下面，URL请求的参数是被暴露的。在HTTPS下面，URL的请求参数是被加密的。因此，希望需要填写个人信息，及在线支付的网站，都把HTTPS服务器搭建起来。防止私密数据，在网络传输过程中被获取。

#### 转载请注明出处：http://blog.fens.me/nodejs-https-server/

