R和JSON的傻瓜式编程
==========

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-json-rjson/

![R和JSON的傻瓜式编程](http://blog.fens.me/wp-content/uploads/2013/09/rjson.png)

#### 前言

JSON作为一种轻量级数据格式，被大量地应用在各种程序环境中。JSON(JavaScript Object Notation)是Javascript的内嵌的标准对象，同时也是MongoDB的表结构存储类型。JSON是半结构化的，可以表达出丰富的文档含义。JSON文档比XML文档要少很多，更适合于网络传输。

早期R语言编程很少会用到JSON，但随着R语言的壮大，R也在伸向各种领域，JSON就是与其他领域的一个交点。

如何让R语言傻瓜式花转JSON？请看正文介绍。

#### 目录

1. rjson包介绍
2. RJSONIO包介绍
3. 自定义JSON的实现
4. toJSON性能比较: 我的测试结果rjson比RJSONIO更高效

## 1. rjson包介绍

rjson是一个R语言与json进行转的包，是一个非常简单的包，支持用 C类库转型和R语言本身转型两种方式。

rjson库，提供的函数只有3个，fromJSON(), newJSONParser(), toJSON()。 下面我们将介绍rjson库如何使用。

### 我的系统环境

* Win7: x86_64-w64-mingw32/x64 (64-bit)
* R: version 3.0.1 (2013-05-16)

### 1). 安装和加载rjson

```{bash}
install.packages("rjson")
library(rjson)
```

新建一个JSON文件: fin0.json

```{bash}
~ vi fin0.json

{
    "table1": {
        "time": "130911",
        "data": {
            "code": [
                "TF1312",
                "TF1403",
                "TF1406"
            ],
            "rt_time": [
                130911,
                130911,
                130911
            ]
        }
    },
    "table2": {
        "time": "130911",
        "data": {
            "contract": [
                "TF1312",
                "TF1312",
                "TF1403"
            ],
            "jtid": [
                99,
                65,
                21
            ]
        }
    }
}
```

### 2). fromJSON() 从JSON到R

从fin0.json文件中，读取JSON并解析为R语言对象。

```{bash}
> json_data <- fromJSON(paste(readLines("fin0.json"), collapse=""))
> json_data
$table1
$table1$time
[1] "130911"

$table1$data
$table1$data$code
[1] "TF1312" "TF1403" "TF1406"

$table1$data$rt_time
[1] 130911 130911 130911

$table2
$table2$time
[1] "130911"

$table2$data
$table2$data$contract
[1] "TF1312" "TF1312" "TF1403"

$table2$data$jtid
[1] 99 65 21
```

我们看到原JSON，除最内层都被解析为R的list类型，最内层则是向量类型。

在R对象结构中取叶子节点, json.table1.data.code[0]

```{bash}
> json_data$table1$data$code
[1] "TF1312" "TF1403" "TF1406"

> json_data$table1$data$code[1]
[1] "TF1312"
```

### 3). toJSON() 从R到JSON

把R对象序列化为JSON串，还以刚才的json_data为例

```{bash}
> json_str<-toJSON(json_data)

> print(json_str)
[1] "{\"table1\":{\"time\":\"130911\",\"data\":{\"code\":[\"TF1312\",\"TF1403\",\"TF1406\"],\"rt_time\":[130911,130911,130911]}},\"table2\":{\"time\":\"130911\",\"data\":{\"contract\":[\"TF1312\",\"TF1312\",\"TF1403\"],\"jtid\":[99,65,21]}}}"

> cat(json_str)
{"table1":{"time":"130911","data":{"code":["TF1312","TF1403","TF1406"],"rt_time":[130911,130911,130911]}},"table2":{"time":"130911","data":{"contract":["TF1312","TF1312","TF1403"],"jtid":[99,65,21]}}}
```

我们只要使用toJSON()函数，就可以实现R对象向JSON的转成。

如果用print结果就是带转义的输出(\”)，直接用cat打印就是标准的JSON串格式。

把JSON输出到文件：fin0_out.json, 有2种方法：

1. writeLines()
2. sink()

```{bash}

# writeLines
> writeLines(json_str, "fin0_out1.json")

# sink
> sink("fin0_out2.json")
> cat(json_str)
> sink()
```

虽然写法不同，输出结果是一个样的，writeLines最后新建一个空行。

```{bash}
{"table1":{"time":"130911","data":{"code":["TF1312","TF1403","TF1406"],"rt_time":[130911,130911,130911]}},"table2":{"time":"130911","data":{"contract":["TF1312","TF1312","TF1403"],"jtid":[99,65,21]}}}
```

### 4). C库和R库转型 性能测试

我们对fromJSON进行性能测试

```{bash}
> system.time( y <- fromJSON(json_str,method="C") )
用户 系统 流逝 
   0    0    0 
> system.time( y2 <- fromJSON(json_str,method = "R") )
用户 系统 流逝 
0.02 0.00 0.02
> system.time( y3 <- fromJSON(json_str) )
用户 系统 流逝 
   0    0    0  
```

我们可以看到，C库比R库会快，因为数据量很小，所以0.02并不明显。当JSON串很大的时候，这个差距就会变得相当的大了。

另外，fromJSON默认使用的C库的方法，所以我们平时处理不用加method='C'的参数。

toJSON的性能测试

```{bash}
> system.time( y <- toJSON(json_data,method="C") )
用户 系统 流逝 
   0    0    0 
> system.time( y2 <- toJSON(json_data,method = "R") )
用户 系统 流逝 
0.02 0.00 0.01 
> system.time( y3 <- toJSON(json_data) )
用户 系统 流逝 
   0    0    0 
```

同上的解释。

## 2. RJSONIO包介绍

RJSONIO包，提供了2个主要的操作，把JSON串反序列化成R对象，把R对象序列化成JSON串，两个主要的函数fromJSON(), toJSON()，还包括几个辅助函数asJSVars(), basicJSONHandler(), Bob(), isValidJSON(), readJSONStream()。

RJSONIO包开发，是解决了rjson包序列化大对象慢的问题。RJSONIO依赖于底层的C语言类库libjson。

### 1). 安装和加载RJSONIO

```{bash}
install.packages("RJSONIO")
library(RJSONIO)
```

### 2). fromJSON() 从JSON到R

同rjson一样，测试fromJSON函数

```{bash}
> json_data <- fromJSON(paste(readLines("fin0.json"), collapse=""))
> json_data
$table1
$table1$time
[1] "130911"

$table1$data
$table1$data$code
[1] "TF1312" "TF1403" "TF1406"

$table1$data$rt_time
[1] 130911 130911 130911

$table2
$table2$time
[1] "130911"

$table2$data
$table2$data$contract
[1] "TF1312" "TF1312" "TF1403"

$table2$data$jtid
[1] 99 65 21
```

我们发现与 rjson的解析结果是一样，都是基于list的

取叶子节点：

```{bash}
> json_data$table1$data$code
[1] "TF1312" "TF1403" "TF1406"

> json_data$table1$data$code[1]
[1] "TF1312"
```

### 3). toJSON() 从R到JSON

toJSON测试

```{bash}
> json_str<-toJSON(json_data)

> print(json_str)
[1] "{\n \"table1\": {\n \"time\": \"130911\",\n\"data\": {\n \"code\": [ \"TF1312\", \"TF1403\", \"TF1406\" ],\n\"rt_time\": [ 1.3091e+05, 1.3091e+05, 1.3091e+05 ] \n} \n},\n\"table2\": {\n \"time\": \"130911\",\n\"data\": {\n \"contract\": [ \"TF1312\", \"TF1312\", \"TF1403\" ],\n\"jtid\": [     99,     65,     21 ] \n} \n} \n}"

> cat(json_str)
{
 "table1": {
 "time": "130911",
"data": {
 "code": [ "TF1312", "TF1403", "TF1406" ],
"rt_time": [ 1.3091e+05, 1.3091e+05, 1.3091e+05 ] 
} 
},
"table2": {
 "time": "130911",
"data": {
 "contract": [ "TF1312", "TF1312", "TF1403" ],
"jtid": [     99,     65,     21 ] 
} 
} 
}
```

toJSON函数输出与rjson是不一样的，这个输出是格式化的。

输出到文件：

```{bash}
> writeLines(json_str, "fin0_io.json")
```

文件结果：

```{bash}
{
 "table1": {
 "time": "130911",
"data": {
 "code": [ "TF1312", "TF1403", "TF1406" ],
"rt_time": [ 1.3091e+05, 1.3091e+05, 1.3091e+05 ] 
} 
},
"table2": {
 "time": "130911",
"data": {
 "contract": [ "TF1312", "TF1312", "TF1403" ],
"jtid": [     99,     65,     21 ] 
} 
} 
}
```

### 4). isValidJSON() 验证JSON是否合法

验证JSON的格式，是否合法。

```{bash}
> isValidJSON(json_str)
Error in file(con, "r") : cannot open the connection

> isValidJSON(json_str,TRUE)
[1] TRUE

> isValidJSON(I('{"foo" : "bar"}'))
[1] TRUE
> isValidJSON(I('{foo : "bar"}'))
[1] FALSE
```

### 5). asJSVars() 转换为Javascript变量格式

```{bash}
> cat(asJSVars( a = 1:10, myMatrix = matrix(1:15, 3, 5)))
a = [ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 ] ;

myMatrix = [ [ 1, 4, 7, 10, 13 ],
           [ 2, 5, 8, 11, 14 ],
           [ 3, 6, 9, 12, 15 ] ] ;
```

得到JS两个变量，数组a和二维数组myMatrix.

## 3. 自定义JSON的实现

我们把R的data.frame对象转成我们定义的JSON格式。

下面JSON的定义格式

```{bash}
[
    {
        "code": "TF1312",
        "rt_time": "152929",
        "rt_latest": 93.76,
        "rt_bid1": 93.76,
        "rt_ask1": 90.76,
        "rt_bsize1": 2,
        "rt_asize1": 100,
        "optionValue": -0.4,
        "diffValue": 0.6
    }
]
```

R语言data.frame对象

```{bash}
df<-data.frame(
  code=c('TF1312','TF1310','TF1313'),
  rt_time=c("152929","152929","152929"),
  rt_latest=c(93.76,93.76,93.76),
  rt_bid1=c(93.76,93.76,93.76),
  rt_ask1=c(90.76,90.76,90.76),
  rt_bsize1=c(2,3,1),
  rt_asize1=c(100,1,11),
  optionValue=c(-0.4,0.2,-0.1),
  diffValue=c(0.6,0.6,0.5)
)

> df
    code rt_time rt_latest rt_bid1 rt_ask1 rt_bsize1 rt_asize1 optionValue diffValue
1 TF1312  152929     93.76   93.76   90.76         2       100        -0.4       0.6
2 TF1310  152929     93.76   93.76   90.76         3         1         0.2       0.6
3 TF1313  152929     93.76   93.76   90.76         1        11        -0.1       0.5
```

直接使用toJSON，输出的JSON串是按列组合成了数组，并不是我们想要的。

```{bash}
> cat(toJSON(df))
{
 "code": [ "TF1312", "TF1310", "TF1313" ],
"rt_time": [ "152929", "152929", "152929" ],
"rt_latest": [  93.76,  93.76,  93.76 ],
"rt_bid1": [  93.76,  93.76,  93.76 ],
"rt_ask1": [  90.76,  90.76,  90.76 ],
"rt_bsize1": [      2,      3,      1 ],
"rt_asize1": [    100,      1,     11 ],
"optionValue": [   -0.4,    0.2,   -0.1 ],
"diffValue": [    0.6,    0.6,    0.5 ] 
}
```

对data.frame进行数据处理

```{bash}
library(plyr)
> cat(toJSON(unname(alply(df, 1, identity))))
[
 {
 "code": "TF1312",
"rt_time": "152929",
"rt_latest":  93.76,
"rt_bid1":  93.76,
"rt_ask1":  90.76,
"rt_bsize1":      2,
"rt_asize1":    100,
"optionValue":   -0.4,
"diffValue":    0.6 
},
{
 "code": "TF1310",
"rt_time": "152929",
"rt_latest":  93.76,
"rt_bid1":  93.76,
"rt_ask1":  90.76,
"rt_bsize1":      3,
"rt_asize1":      1,
"optionValue":    0.2,
"diffValue":    0.6 
},
{
 "code": "TF1313",
"rt_time": "152929",
"rt_latest":  93.76,
"rt_bid1":  93.76,
"rt_ask1":  90.76,
"rt_bsize1":      1,
"rt_asize1":     11,
"optionValue":   -0.1,
"diffValue":    0.5 
} 
]
```

这回就对了，正是我希望的按行输出的结果！！通过alply函数做了变换。

## 4. JSON性能比较

性能比较我们做2组测试：

1. rjson和RJSONIO 对大对象进行序列化(toJSON)测试
2. RJSONIO包，序列化(toJSON) 列式输出和行式输出的测试

### 1). rjson和RJSONIO 对大对象进行序列化(toJSON)测试

创建一个rjson测试脚本，在命令行启动。

```{bash}
> library(rjson)
>
> df<-data.frame(
+   a=rep(letters,10000),
+   b=rnorm(260000),
+   c=as.factor(Sys.Date()-rep(1:260000))
+ )
>
> system.time(rjson::toJSON(df))
1.01 0.02 1.03
> system.time(rjson::toJSON(df))
1.01 0.03 1.04
> system.time(rjson::toJSON(df))
0.98 0.05 1.03
```

同样，再创建一个RJSONIO测试脚本，在命令行启动。

```{bash}
> library(RJSONIO)
>
> df<-data.frame(
+   a=rep(letters,10000),
+   b=rnorm(260000),
+   c=as.factor(Sys.Date()-rep(1:260000))
+ )
>
> system.time(RJSONIO::toJSON(df))
2.23 0.02 2.24
> system.time(RJSONIO::toJSON(df))
2.30 0.00 2.29
> system.time(RJSONIO::toJSON(df))
2.25 0.01 2.26
```

对比结果发现，rjson的性能优于RJSONIO。

### 2). rjson和RJSONIO，序列化(toJSON) 列式输出和行式输出的测试

创建一个rjson测试脚本，在命令行启动。

```{bash}
> library(rjson)
> library(plyr)
>
> df<-data.frame(
+   a=rep(letters,100),
+   b=rnorm(2600),
+   c=as.factor(Sys.Date()-rep(1:2600))
+ )
>
> system.time(rjson::toJSON(df))
0.01 0.00 0.02
> system.time(rjson::toJSON(df))
0.01 0.00 0.02

> system.time(rjson::toJSON(unname(alply(df, 1, identity))))
1.55 0.02 1.56
> system.time(rjson::toJSON(unname(alply(df, 1, identity))))
0.83 0.00 0.83
```

同样，再创建一个RJSONIO测试脚本，在命令行启动。

```{bash}
> library(RJSONIO)
> library(plyr)
>
> df<-data.frame(
+   a=rep(letters,100),
+   b=rnorm(2600),
+   c=as.factor(Sys.Date()-rep(1:2600))
+ )
>
> system.time(RJSONIO::toJSON(df))
0.03 0.00 0.03
> system.time(RJSONIO::toJSON(df))
0.04 0.00 0.03

> system.time(RJSONIO::toJSON(unname(alply(df, 1, identity))))
2.82 0.02 2.84
> system.time(RJSONIO::toJSON(unname(alply(df, 1, identity))))
2.06 0.00 2.06
```

通过测试我们发现，用toJSON直接列式输出，比行式输出要高效的多。同时，rjson要比RJSONIO高效。

#### 转载请注明出处：http://blog.fens.me/r-json-rjson/

