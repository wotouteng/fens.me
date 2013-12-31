了解google的指令集
=====

[我的博客我的SEO系列文章](http://blog.fens.me/series-seo/)，将介绍如何利用搜索引擎优化技术，来提高我的博客的排名，一方面把知识分享给更多的人，另一方面通过网站流量做些转化的尝试。

写博客文章已经有3个月，70篇博文，9大系列，覆盖R语言，Hadoop，RHadoop，NoSQL，MongoDB，Ubuntu，VPS，NodeJS 等领域。 有一部分文章已经被google和baidu收录，并且自然搜索排名的位置比较靠前。通过保证文章的质量，提高自然搜索的排名，是没有捷径的。但由于博客没有做任何推广，所以PR一直是0，搜索引擎速带来的流量也是非常有限的。优化必然带来提升。

做IT技术不仅要扎根深入，广度也要有所积累。所以今天就开始自己动手,优化自己的博客，让网站流量提高20倍吧…..

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/google-command/

![了解google的指令集](http://blog.fens.me/wp-content/uploads/2013/08/google-command.png)

#### 前言

Google是全球最大的搜索引擎，我每天都要在Google上面进行搜索。大部分情况是输入关键字，然后看到模糊匹配的结果。这仅仅是Google搜索的冰山一角，Google还提供了很多的高级搜索指令，方便地帮我们定位到想找的资源页面。这些高级指令更贴心的比喻，就像是“快捷键”一样。还有人说，这些高级指令是黑客的必修课，是发现漏洞的武器。今天就让我们体验一把，Google搜索的神奇。

#### 目录

1. 高级搜索命令概况
2. 常用指令:
  *双引号,减号,星号,inurl,intitle,allintitle,filetype,site,link,related*
3. 没什么用指令：
 *info,insubject,movie,phonebook,safesearch,source,stocks,datarange,ext,weather,tq*
4. 参考资料

## 1. 高级搜索命令概况

按搜索功能进行的分类

<table>
   <tr>
      <td>搜索服务</td>
      <td>搜索指令</td>
   </tr>
   <tr>
      <td>网页搜索</td>
      <td>allinanchor:, allintext:, allintitle:, allinurl:, cache:, define:, filetype:, id:, inanchor:, info:,  intext:, intitle:, inurl:,link:, related:, site:</td>
   </tr>
   <tr>
      <td>图片搜索</td>
      <td>allintitle:, allinurl:, filetype:, inurl:, intitle:, site:</td>
   </tr>
   <tr>
      <td>分组搜索</td>
      <td>allintext:, allintitle:, author:, group:, insubject:, intext:, intitle:</td>
   </tr>
   <tr>
      <td>目录搜索</td>
      <td>allintext:, allintitle:, allinurl:, ext:, filetype:, intext:, intitle:, inurl:</td>
   </tr>
   <tr>
      <td>新闻搜索</td>
      <td>allintext:, allintitle:, allinurl:, intext:, intitle:, inurl:, location:, source:</td>
   </tr>
   <tr>
      <td>产品搜索</td>
      <td>allintext:, allintitle:</td>
   </tr>
</table>

下面将具体介绍，每个搜索指令，并以常用，不常用进行分类。

## 2. 常用指令

### 1) 双引号

把搜索词放在双引号中，代表完全匹配搜索。

例如：”rhadoop实践”

![](http://blog.fens.me/wp-content/uploads/2013/08/seo1.png)

结果都是cos.name，虽然我的博客也相同的文章，但由于cos.name的权重比大，会显示cos.name的。

### 2) 减号

减号（-）表示搜索引擎搜索不包括减号后面词的页面。(前面必须是空格，减号后面没有空格，紧跟着需要排除的词).

例如:”rhadoop实践” -cos.name ，排除cos.name的域名。

![](http://blog.fens.me/wp-content/uploads/2013/08/seo2.png)

在排除cos.name的域名之后，我的博客被放到第一的位置。

### 3) 星号

星号（*）是通配符，用在搜索引擎中，匹配任意的字符。

例如：rhadoo*实践, 用*号替代的文字会自动的匹配上面

![](http://blog.fens.me/wp-content/uploads/2013/08/seo3.png)

### 4) inurl
inurl:搜索词在URL上面的

例如：inurl:rhadoop实践

![](http://blog.fens.me/wp-content/uploads/2013/08/seo4.png)

匹配后只有2条记录，只有两个页面URL中，包含rhadoop实践关键字。

### 5) intitle
intitle:搜索词是页面title中包含关键词

例如：intitle:rhadoop实践

![](http://blog.fens.me/wp-content/uploads/2013/08/seo5.png)

回到了cos.name的域名。

### 6) allintitle
allintitle:搜索返回的是页面标题中包含多组关键词的文件。

例如：allintitle:rhadoop实践 粉丝

![](http://blog.fens.me/wp-content/uploads/2013/08/seo6.png)

回到了我的博客

### 7) filetype
filetype: 用于搜索特定文件的格式，用于搜索特定的资源，比如PDF电子书、word文件等非常有用。

例如：filetype:pdf rhadoop

![](http://blog.fens.me/wp-content/uploads/2013/08/seo7.png)

这样就找到了当时准备rhadoop沙龙的PDF

### 8) site
site:针对某个域名进行搜索

例如：site:fens.me rhadoop

![](http://blog.fens.me/wp-content/uploads/2013/08/seo8.png)

还是我的博客内容。

### 9) link
link:查找某网站的反向链接，包括内部链接，外部链接

例如：link:fens.me
惭愧的是，Google并没记录我的博客的反链。

### 10) related
related:与网站内容相关的页面

例如：related:fens.me

依然没有返回结果，因为我的博客还没有明确的定位。

查一下统计之都的网站：related:cos.name

![](http://blog.fens.me/wp-content/uploads/2013/08/seo9.png)

最相关的是创始人：谢益辉的博客。

## 3. 没什么用的指令

### 1) info
提交info:url，将会显示需要查询网站的一些信息。

例如：info:fens.me

### 2) insubject
insubject: 当我们用insubject进行查询的时候，Google会限制论坛搜索结果仅是那些在主题里边包含了查询关键词的网面。［insubject:”falling asleep”］，提交这个查询，Google会返回在文章主题里边包含了”falling asleep”的文章。
例如：insubject:fens.me

### 3) movie
movie: 当我们用movie提交查询的时候，Google会返回跟查询关键词相关的电影信息。

### 4) phonebook
phonebook: 当我们用phonebook进行查询的时候，Google会返回美国当地跟查询关键词相关的电话信息。例:phonebook:fens.me

### 5) safesearch
safesearch: 用safesearch提交查询的时候，Google会过滤你搜索的结果，其中过滤的内容可能包括一些色情的，暴力，赌博性质的，还有传染病毒的网页。但是它不是百分之百确保安全的。例，［safesearch:breasts］。

### 6) source
source: 当用source提交查询的时候，Google新闻会限制我们的查询仅是那些我们指定了特定ID或新闻源的网址。例［election source:new_york_times］，提交这个查询，Google将会显示纽约时报包含了查询关键词”election”的相关文章。（我们也可以通过Google news高级搜索完成查询）

### 7) stocks
stocks: 当我们用stocks提交查询的时候，Google会返回跟查询关键词相关的股票信息，这些信息一般来自于其它一些专业的财经网站。例如：stocks:GOOGLE

### 8) datarange
datarange: 当我们使用datarange进行查询的时候，Google会将查询结果限制在一个特定的时间段内，这个时间相对于网站来说，是按网站被google收录的时间算的。例　”Geri Halliwell” “Spice Girls” daterange:2450958-2450968　。这里的时间日期格式是按天文学的儒略日。（这个搜索语法Google并不推荐使用，因为它会返回一些莫名其妙的东西）

### 9) ext
ext: 这是一个没有证实的语法，可以用于filetype:查找扩散名为ext的文件。

### 10) weather
weather: 当我们用weather提交查询的时候，如果我们指出一个Google可以识别的地区或城市，Google会返回该地区或城市当前的天气状况。

例如：weather:beijing

![](http://blog.fens.me/wp-content/uploads/2013/08/s1.png)

### 11) tq同weather
tq: 如果想查某个地方的天气如何,我们只要在Google搜索框中输入”城市名称 Tq”就可以查询到这个城市的天状况.例”北京 tq”,当然tq也可以用汉字的天气代替。

## 4. 参考资料

* 下载：Google常用指令卡片
* googleguide: http://www.googleguide.com/advanced_operators_reference.html
* Google命令语法大全: http://www.ssoooo.com/google-command.html

#### 转载请注明出处：http://blog.fens.me/google-command/

