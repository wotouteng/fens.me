R语言本地缓存memoise
========

[R的极客理想系列文章](http://blog.fens.me/series-r/)，涵盖了R的思想，使用，工具，创新等的一系列要点，以我个人的学习和体验去诠释R的强大。

R语言作为统计学一门语言，一直在小众领域闪耀着光芒。直到大数据的爆发，R语言变成了一门炙手可热的数据分析的利器。随着越来越多的工程背景的人的加入，R语言的社区在迅速扩大成长。现在已不仅仅是统计领域，教育，银行，电商，互联网….都在使用R语言。

要成为有理想的极客，我们不能停留在语法上，要掌握牢固的数学，概率，统计知识，同时还要有创新精神，把R语言发挥到各个领域。让我们一起动起来吧，开始R的极客理想。

#### 关于作者：

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/r-cache-memoise/

![R语言本地缓存memoise](http://blog.fens.me/wp-content/uploads/2013/11/memoise-r.png)

#### 前言

缓存技术在计算机系统中运用地非常广泛，特别是对于重复性计算，缓存能为我们节省大量的CPU时间，可能是99%。R语言以统计计算著名，但其中很多算法包都是在进行大量重复的计算。

优化正再进行，改变已经开始。以Hadley Wickham为代表的R语言领军人物，正在让R快起来！你感觉到了吗？！

#### 目录

1. memoise介绍
2. memoise安装
3. memoise的API介绍
4. memoise使用
5. memoise源代码分析

## 1. memoise介绍

memoise是一个简单的缓存包，主要用来减少重复计算，从而提升CPU性能。当你用相同的参数执行计算的时候，你会得到之前计算过的结果，而不是重算一遍。

缓存技术对于有并发访问的应用来说，是性价比最高的性能提升方案。

注：memoise包是“Hadley Wickham”大神的作品！

memoise的发布页：http://cran.r-project.org/web/packages/memoise/index.html

## 2. memoise安装

### 系统环境

* Win7 64bit
* R: 3.0.1 x86_64-w64-mingw32/x64 b4bit

### memoise安装

```{bash}
~R

> install.packages("memoise")
trying URL 'http://mirror.bjtu.edu.cn/cran/bin/windows/contrib/3.0/memoise_0.1.zip'
Content type 'application/zip' length 10816 bytes (10 Kb)
opened URL
downloaded 10 Kb

package ‘memoise’ successfully unpacked and MD5 sums checked
```

### memoise加载

```{bash}
> library(memoise)
```

## 3. memoise的API介绍

非常简单的API列表，只有2个函数。

* forget: 重置缓存函数
* memoize: 定义缓存函数

## 4. memoise使用

### 缓存测试

```{bash}
#定义缓存函数
> fun <- memoise(function(x) { Sys.sleep(1); runif(1) })

#第一次执行fun函数
> system.time(print(fun()))
[1] 0.05983416
用户 系统 流逝 
   0    0    1

#第二次执行fun函数 
> system.time(print(fun()))
[1] 0.05983416
用户 系统 流逝 
   0    0    0 

#重置缓存函数
> forget(fun)
[1] TRUE

#第三次执行fun函数 
> system.time(print(fun()))
[1] 0.6001663
用户 系统 流逝 
   0    0    1 
```

* 1. 定义缓存函数memoise
* 2. 第一次执行fun函数, 等待sleep(1)
* 3. 第二次执行fun函数, 无等待，直接从缓存中返回结果
* 4. 重置缓存函数forget
* 5. 第三次执行fun函数, 由于fun被重置，返回2，等待sleep(1)

## 5. memoise源代码分析

### 1). memoise函数

* 1. new_cache创建新的缓存空间，给f函数
* 2. 生成f函数的hash值，作为key
* 3. 返回缓存后的,f函数引入

```{bash}

memoise <- memoize <- function(f) {
  cache <- new_cache()
  
  memo_f <- function(...) {
    hash <- digest(list(...))
    
    if (cache$has_key(hash)) {
      cache$get(hash)
    } else {
      res <- f(...)
      cache$set(hash, res)
      res
    }
  }
  attr(memo_f, "memoised") <- TRUE
  return(memo_f)
}
```

### 2). forget函数

* 1. 检查环境中，是否缓存了f函数
* 2. 如果有f函数的缓存，则清空f函数的缓存值

```{bash}
forget <- function(f) {
  if (!is.function(f)) return(FALSE)
  
  env <- environment(f)
  if (!exists("cache", env, inherits = FALSE)) return(FALSE)
  
  cache <- get("cache", env)
  cache$reset()
  
  TRUE
}
```

### 3). 私有函数：new_cache函数

* 1. 在new_cache函数里，定义cache对象，保存在env的环境中
* 2. 通过new_cache函数，构造list类型对象，包括reset,set,get,has_key,keys方法
* 3. 通过list对象，对cache对象进行访问操作

```{bash}
new_cache <- function() {
  
  cache <- NULL
  cache_reset <- function() {
    cache <<- new.env(TRUE, emptyenv())
  }
  
  cache_set <- function(key, value) {
    assign(key, value, env = cache)
  }
  
  cache_get <- function(key) {
    get(key, env = cache, inherits = FALSE)
  }
  
  cache_has_key <- function(key) {
    exists(key, env = cache, inherits = FALSE)
  }
  
  cache_reset()
  list(
    reset = cache_reset, 
    set = cache_set, 
    get = cache_get,
    has_key = cache_has_key,
    keys = function() ls(cache)
  )
}
```

#### 转载请注明出处：http://blog.fens.me/r-cache-memoise/

