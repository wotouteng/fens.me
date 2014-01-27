30秒制作幻灯片 Cleaver
======

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-slide-cleaver/

![30秒制作幻灯片 Cleaver](http://blog.fens.me/wp-content/uploads/2013/12/nodejs-cleaver.png)

#### 前言

互联网时代，以浏览器作为入口，已经有越来越多的桌面应用被web应用所取代。微软最赚钱的Office办公软件，也正在被免费的web应用所吞噬。

Cleaver基于HTML5，用最短的时间做出超炫幻灯片。你还会坚持PowerPoint吗？

#### 目录

1. Cleaver介绍
2. Cleaver安装
3. Cleaver基本使用
4. Cleaver配置选项option
5. Cleaver的正文内容
6. 用Cleaver制作一个幻灯片

## 1. Cleaver介绍

如果你已经有了一个Markdown的文档，30秒就可以制作成幻灯片。Cleaver是为Hacker准备的工具。

Cleaver的官方发布页：http://jdan.github.io/cleaver/

## 2. Cleaver安装

### 系统环境

* Linux: Ubuntu 12.04 LTS 64bit
* node: v0.6.12
* npm: 1.1.4

### 通过nodejs安装Cleaver

```{bash}

~ cd /home/conan/nodejs
~ mkdir nodejs-cleaver && cd nodejs-cleaver
~ sudo npm install cleaver
cleaver@0.5.3 node_modules\cleaver
├── q@0.9.6
├── marked@0.2.10
├── highlight.js@7.3.0
├── mustache@0.7.0
├── optimist@0.3.5 (wordwrap@0.0.2)
└── js-yaml@2.1.0 (esprima@1.0.4, argparse@0.1.15)
```

### 全局安装Cleaver

```{bash}
~ sudo npm install cleaver -g

/usr/local/bin/cleaver -> /usr/local/lib/node_modules/cleaver/bin/cleaver
npm WARN package.json cookie-signature@1.0.1 No repository field.
npm WARN package.json methods@0.0.1 No repository field.
npm WARN package.json range-parser@0.0.4 No repository field.
npm WARN package.json dateformat@1.0.2-1.2.3 No repository field.
npm WARN package.json batch@0.4.0 No repository field.
npm WARN package.json ms@0.6.1 No repository field.
npm WARN package.json bytes@0.2.1 No repository field.
npm WARN package.json bytes@0.2.0 No repository field.
npm WARN package.json pause@0.0.1 No repository field.
npm WARN package.json uid2@0.0.2 No repository field.
npm WARN package.json eyes@0.1.8 No repository field.
npm WARN package.json policyfile@0.0.4 'repositories' (plural) Not supported.
npm WARN package.json Please pick one as the 'repository' field
npm WARN package.json assert-plus@0.1.2 No repository field.
npm WARN package.json ctype@0.5.2 No repository field.
npm WARN package.json eventemitter2@0.4.11 'repositories' (plural) Not supported.
npm WARN package.json Please pick one as the 'repository' field
npm WARN package.json cheerio-select@0.0.3 No repository field.
npm WARN package.json github-url-from-git@1.1.1 No repository field.
cleaver@0.5.3 /usr/local/lib/node_modules/cleaver
├── marked@0.2.10
├── q@0.9.6
├── highlight.js@7.3.0
├── mustache@0.7.0
├── optimist@0.3.5 (wordwrap@0.0.2)
└── js-yaml@2.1.0 (esprima@1.0.4, argparse@0.1.15)
```

注：cleaver不支持win系统

## 3. Cleaver基本使用

### 执行cleaver命令，解析一个markdown文件

用cleaver自带的例子

```{bash}
~ cleaver node_modules/cleaver/examples/basic.md

~ ls -l
-rw-rw-r-- 1 conan conan 12211 12月  4 15:39 basic.html
drwxr-xr-x 4 conan conan  4096 12月  4 15:37 node_modules

~ firefox basic.html
```

![](http://blog.fens.me/wp-content/uploads/2013/12/cleaver-firefox.png)

### 查看文件：basic.md

```{bash}
~ vi node_modules/cleaver/examples/basic.md

title: Basic Example
author:
name: Jordan Scales
twitter: jdan
url: http://jordanscales.com
output: basic.html

--

# Cleaver 101
## A first look at quick HTML presentations

--

### A textual example

Content can be written in **Markdown!** New lines no longer need two angle brackets.

This will be in a separate paragraph.

<img src="http://whatismarkdown.com/workspace/img/logo.gif" alt="Drawing" style="width: 150px;"/>

![markdown-logo](logo.gif)
<img src="logo.gif" />

[Here's a link](http://google.com).

--

### A list of things

* Item 1
* Item B
* Item gamma

No need for multiple templates! [Another link](http://google.com).

--

### Unicode

* 林花謝了春紅 太匆匆
* 胭脂淚 留人醉 幾時重
* Matching Pairs «»‹› “”‘’「」〈〉《》〔〕
* Greek αβγδ εζηθ ικλμ νξοπ ρςτυ φχψω
* currency ¤ $ ¢ € ₠ £ ¥

--

### A code example

```javascript
// cool looking code
var func = function (arg1) {
return function (arg2) {
return "arg1: " + arg1 + "arg2: " + arg2;
};
};

console.log(func(1)(2)); // result is three
 ```

And here is some `inline code` to check out.
```

## 4. Cleaver配置选项option

```{bash}
title: Basic Example
author:
  name: Jordan Scales
  twitter: jdan
  url: http://jordanscales.com
output: basic.html
```

1). title: HTML的标题名

2). author: 作者信息,在最后一页显示

包括下面信息：

* name: 名字
* url: 个人网站
* twitter: 微博
* email: 邮件地址

3). theme: 皮肤(theme: jdan/cleaver-retro)

4). style: css样式表(style: css/main.css)

5). output: 生成的HTML文件名(output: basic.html)

6). controls: 控制按钮(controls: true)

7). progress: 顶部显示进程条(progress: true)

8). agenda: 生成一个目录页(progress: false)

9). encoding: 文档的字符编码(encoding: utf-8)

10). template: 设置每张slide的模板(template: template/slide.mustache)

11). layout: 设置HTML模板(template: template/layout.mustache)

## 5. Cleaver的正文内容

Cleaver仅支持Markdown语法的，通过Markdown构建网页内容，Markdown官方网站：http://daringfireball.net/projects/markdown/

需要注意的是，Cleaver以” — “(两个中横线)做为分页的标志，其他语法都遵循Markdown规则。

## 6. 用Cleaver制作一个幻灯片

### 新建文件: readme.md

```{bash}

~ vi readme.md

title: My Cleaver demo!
author:
    name: 张丹(DanZhang)
    twitter: Conan_Z
    url: http://blog.fens.me/nodejs-slide-cleaver/
output: cleaver.html
encoding: utf-8

--

# 前言

互联网时代，以浏览器作为入口，已经有越来越多的桌面应用被web应用所取代。微软最赚钱的Office办公软件，也正在被免费的w                                                                                   eb应用所吞噬。

Cleaver基于HTML5，用最短的时间做出超炫幻灯片。你还会坚持PowerPoint吗？

--

# 目录

1. Cleaver介绍
2. Cleaver安装
3. Cleaver基本使用
4. Cleaver配置选项option
5. 用Cleaver制作一个幻灯片

--

## 1. Cleaver介绍

30-second Slideshows for Hackers. http://jdan.github.io/cleaver/

--

## 2. Cleaver安装

```bash
~ cd /home/conan/nodejs
~ mkdir nodejs-cleaver && cd nodejs-cleaver
~ sudo npm install cleaver
cleaver@0.5.3 node_modules\cleaver
├── q@0.9.6
├── marked@0.2.10
├── highlight.js@7.3.0
├── mustache@0.7.0
├── optimist@0.3.5 (wordwrap@0.0.2)
└── js-yaml@2.1.0 (esprima@1.0.4, argparse@0.1.15)
 ```

--

## 3. Cleaver基本使用

执行cleaver命令，解析一个markdown文件

用cleaver自带的例子
 ```bash
~ cleaver node_modules/cleaver/examples/basic.md

~ ls -l
-rw-rw-r-- 1 conan conan 12211 12月  4 15:39 basic.html
drwxr-xr-x 4 conan conan  4096 12月  4 15:37 node_modules

~ firefox basic.html
 ```

![example](http://blog.fens.me/wp-content/uploads/2013/12/cleaver-firefox.png)

--

## 4. Cleaver配置选项option

--

## 5. 用Cleaver制作一个幻灯片
```

### 执行cleaver生成html

```{bash}
~ cleaver readme.md
```

### 在浏览器中预览

```{bash}
~ firefox cleaver.html
```

![](http://blog.fens.me/wp-content/uploads/2013/12/cleaver-ppt.png)

MS Office PowerPoint 正在被远离。。。。

#### 转载请注明出处：http://blog.fens.me/nodejs-slide-cleaver/

