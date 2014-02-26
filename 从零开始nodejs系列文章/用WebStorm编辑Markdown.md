用WebStorm编辑Markdown
=========

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/webstorm-markdown/

![用WebStorm编辑Markdown](http://blog.fens.me/wp-content/uploads/2014/02/webstorm-markdown-title.png)

#### 前言

WebStorm是一个非常高效的开发工具，特别对于Web前端的开发。Markdown流行，因为其简化了HTML的语法，去掉了表单等复杂标签，以极简的语法只做网页展现。在WebStorem中编辑Markdown文档，是一种普遍的应用需求。

#### 目录

1. Markdown介绍
2. 在WebStorem中安装Markdown插件
3. 解决Markdown插件配置的问题

## 1. Markdown介绍

Markdown 是一种轻量级标记语言，创始人为约翰·格鲁伯（John Gruber）和亚伦·斯沃茨（Aaron Swartz）。它允许人们“使用易读易写的纯文本格式编写文档，然后转换成有效的XHTML(或者HTML)文档”。

Markdown的语法，对于没有编程经验的人，2-3个小时也可以掌握。各种语言基于的Markdown实现，都已经有了商业应用。

* 高效，简约的设计，会让Markdown越走越远
* 开源，免费的Liences，最后有可能会夺取微软Word软件的市场

本文不讲Markdown的语法，请大家自行查询。

Markdown的官方网站：http://daringfireball.net/projects/markdown/

## 2. 在WebStorem中安装Markdown插件

本来在WebStorem中安装Markdown插件很简单的事情，不知道是Idea的版本原因，还是Markdown插件设计者的原因，造成了一些使用问题，需要手动调整一些参数才正确使用。

我在这里也花了一些时间，找到了问题所在，因此写篇文章总结一下。

* WebStorm版本：6.0.1
* Makrdown插件版本：0.9.3

插件安装：

* 手动安装：需要自己下载markdown插件, 下载地址：http://plugins.jetbrains.com/plugin/5970?pr=phpStorm
* 自动安装：在Webstorm中选择: File –> Settings –> Plugins –> Browse repositories –> 搜索markdown

自动安装的截图：

![](http://blog.fens.me/wp-content/uploads/2014/02/webstorm-install.png)

重起Webstorm软件，新建一个test.md文件。

<pre><code>
  APE: 测试Markdown语法
  ========================
  
  Markdown语法
  
  ## INSTALL
  
  #### 公共库安装
  ```{bash}
  npm install
  bower install
  ```
  
  #### 私有库安装
  ```{bash}

  ```
  
  ## API
  
  ## demo
  
  ## License
  
  private
</code></pre>

我们看到，编辑器没有针对 markdown的文件进行适配，显示效果与文本文件无异。

![](http://blog.fens.me/wp-content/uploads/2014/02/markdown-txt.png)

**这就是问题所在！！**

## 3. 解决Markdown插件配置的问题

经过排查发现，问题在于markdown插件没有和.md后缀的文件进行绑定！

查看文件类型的绑定：File –> Settings –> File Types –> Markdown files

![](http://blog.fens.me/wp-content/uploads/2014/02/markdown-files-old.png)

增加*.md类型的绑定

![](http://blog.fens.me/wp-content/uploads/2014/02/markdown-files-add.png)

然后，点击apply。

完成后，我们再打开之前编辑的test.md文件，Markdown的效果已经可以展示了。

![](http://blog.fens.me/wp-content/uploads/2014/02/markdown-show.png)

底部标签text，preview可以在编辑和预览过程中切换效果，而且text也有语法高亮了。

如果想用Webstorm开发AngularJS，请参考文章：[AngularJS最理想开发工具WebStorm](http://blog.fens.me/angularjs-webstorm-ide/)

#### 转载请注明出处：http://blog.fens.me/webstorm-markdown/


