grunt让Nodejs规范起来
============

[从零开始nodejs系列文章](http://blog.fens.me/series-nodejs/)，将介绍如何利Javascript做为服务端脚本，通过Nodejs框架web开发。Nodejs框架是基于V8的引擎，是目前速度最快的Javascript引擎。chrome浏览器就基于V8，同时打开20-30个网页都很流畅。Nodejs标准的web开发框架Express，可以帮助我们迅速建立web站点，比起PHP的开发效率更高，而且学习曲线更低。非常适合小型网站，个性化网站，我们自己的Geek网站！！

#### 关于作者

+ 张丹(Conan), 程序员Java,R,PHP,Javascript
+ weibo：@Conan_Z
+ blog: http://blog.fens.me
+ email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/nodejs-grunt-intro/

![grunt让Nodejs规范起来](http://blog.fens.me/wp-content/uploads/2013/08/grunt.png)

#### 前言

一个应用开发到一定阶段，普遍会遇到一个问题。当功能越来越多，代码量越来越大，bug修复越来越频繁，开发人员一波一波的交替，…..应该用会向着越来越不可控发展。我们不能再准确估计新功能的开发时间，也不知道一个bug修复后是否会引发另一个bug出现。所有的程序开发，都会面临着这样的问题。

C/C++程序通过makefile管理编译测试打包的过程，Java程序通过Maven,Ant实现项目构建管理功能，Python有pip，Ruby有gem。在Nodejs的领域，我们同样需要一个项目构建工具，这就是Grunt。Grunt可以执行像压缩, 编译, 单元测试, 代码检查以及打包发布的任务。

#### 目录

1. Grunt介绍
2. Grunt安装
3. Grunt使用
4. Grunt常用插件：
  + 1).grunt-contrib-uglify：压缩js代码
  + 2).grunt-contrib-concat：合并js文件
  + 3).grunt-contrib-qunit：单元测试
  + 4).grunt-contrib-jshint：js代码检查
  + 5).grunt-contrib-watch：文件监控

## 1. Grunt介绍

Grunt是一个自动化的项目构建工具. 如果你需要重复的执行像压缩, 编译, 单元测试, 代码检查以及打包发布的任务. 那么你可以使用Grunt来处理这些任务, 你所需要做的只是配置好Grunt, 这样能很大程度的简化你的工作.

如果在团队中使用Grunt, 你只需要与其他人员约定好使用Grunt应该规避的问题, 就能够很方便的自动化的处理大部分的常见工作任务, 你所付出的努力几乎为0.

## 2. Grunt安装

Grunt和Grunt插件都是通过npm, Node.js包管理器安装和管理的.

### 我的系统环境

+ win7 64bit
+ Nodejs:v0.10.5
+ Npm:1.2.19

```{bash}
~ D:\workspace\javascript>node -v
v0.10.5

~ D:\workspace\javascript>npm -v
1.2.19
```

在系统中，我们已经安装好了Nodejs和npm。win7安装nodejs请参考文章：[Nodejs开发框架Express3.0开发手记–从零开始](http://blog.fens.me/nodejs-express3/)

### 安装grunt-cli

grunt-cli并不grunt，grunt-cli的作用是管理本地各版本的grunt，让命令行可以直接执行grunt命令。

下面全局安装grunt-cli(-g)

```{bash}

~ D:\workspace\javascript>npm install -g grunt-cli

D:\toolkit\nodejs\grunt -> D:\toolkit\nodejs\node_modules\grunt-cli\bin\grunt
grunt-cli@0.1.9 D:\toolkit\nodejs\node_modules\grunt-cli
├── resolve@0.3.1
├── nopt@1.0.10 (abbrev@1.0.4)
└── findup-sync@0.1.2 (lodash@1.0.1, glob@3.1.21)
```

我们看到grunt-cli似乎做了一个软件链接，把grunt脚本复制到nodejs安装根目录里。

接下来全局安装grunt

```{bash}
~ D:\workspace\javascript>npm install -g grunt

~ D:\workspace\javascript>grunt
grunt-cli: The grunt command line interface. (v0.1.9)

Fatal error: Unable to find local grunt.

If you're seeing this message, either a Gruntfile wasn't found or grunt
hasn't been installed locally to your project. For more information about
installing and configuring grunt, please see the Getting Started guide:

http://gruntjs.com/getting-started

```

执行grunt命令，我们发现系统报错了，提示不能加载本地库。因为，grunt命令执行，是需要当前目录中包括package.json和Gruntfile.js两个文件。

package.json，是npm项目配置文件
Gruntfile.js，是专门用来配置grunt的配置文件

接下来，我们创建一个express3的项目。

```{bash}
~ D:\workspace\javascript>express -e nodejs-grunt
~ D:\workspace\javascript>cd nodejs-grunt && npm install	 
~ D:\workspace\javascript\nodejs-grunt>npm install grunt --save-dev
```

安装-save-dev，就可以，直接把grunt作为devDependencies写入的package.json中。

```{bash}
~ vi package.json

{
  "name": "nodejs-grunt",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "express": "3.2.2",
    "ejs": "*"
  },
  "devDependencies": {
    "grunt": "~0.4.1",
  }
}
```

然后，我们再执行grunt，系统提示缺少Gruntfile文件

```{bash}

~ D:\workspace\javascript\nodejs-grunt>grunt
A valid Gruntfile could not be found. Please see the getting started guide for
more information on how to configure grunt: http://gruntjs.com/getting-started
Fatal error: Unable to find Gruntfile.
```

创建Gruntfile文件

```{bash}
~ vi Gruntfile.js

module.exports = function(grunt) {
  // Project configuration.
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    uglify: {
      options: {
        banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
      },
      build: {
        src: 'src/<%= pkg.name %>.js',
        dest: 'build/<%= pkg.name %>.min.js'
      }
    }
  });
  // Load the plugin that provides the "uglify" task.
  grunt.loadNpmTasks('grunt-contrib-uglify');
  // Default task(s).
  grunt.registerTask('default', ['uglify']);
};
```

再次运行grunt，这时提示是grunt-contrib-uglify包找不到，是Gruntfile.js配置文件中的错误了。

```{bash}
~ D:\workspace\javascript\nodejs-grunt>grunt
>> Local Npm module "grunt-contrib-uglify" not found. Is it installed?
Warning: Task "uglify" not found. Use --force to continue.
```

我们编辑package.js, 在devDependencies中增加grunt-contrib-uglify的依赖库

```{bash}
~ vi package.js
{
  "name": "application-name",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "express": "3.2.2",
    "ejs": "*"
  },
  "devDependencies": {
    "grunt": "~0.4.1"
    "grunt-contrib-uglify": "~0.1.1"
  }
}

~ D:\workspace\javascript\nodejs-grunt>npm install
```

我们创建两个目录src和build，和nodejs-grunt.js的文件

```{bash}

~ D:\workspace\javascript\nodejs-grunt>mkdir src
~ D:\workspace\javascript\nodejs-grunt>mkdir build

~ D:\workspace\javascript\nodejs-grunt>vi src/nodejs-grunt.js
var sayHello = function(name){
	return "Hello " + name;
}
```

我们再执行grunt

```{bash}
~ D:\workspace\javascript\nodejs-grunt>grunt
Running "uglify:build" (uglify) task
File "build/nodejs-grunt.min.js" created.
Uncompressed size: 59 bytes.
Compressed size: 40 bytes gzipped (43 bytes minified).

Done, without errors.
```

grunt运行正常，并且执行了uglify:build的任务。打开build/nodejs-grunt.min.js文件

```{bash}
~ D:\workspace\javascript\nodejs-grunt>vi /build/nodejs-grunt.min.js

/*! nodejs-grunt 2013-08-17 */
var sayHello=function(l){return"Hello "+l};
```

我们可以看到一个新生成的压缩文件nodejs-grunt.min.js。

上面的例子，是一个js文件压缩的例子。

## 3. Grunt使用

我们可以通过help帮助，看一下grunt怎么用。

```{bash}

~ D:\workspace\javascript\nodejs-grunt>grunt --help
Grunt: The JavaScript Task Runner (v0.4.1)

Usage
 grunt [options] [task [task ...]]

Options
    --help, -h  Display this help text.
        --base  Specify an alternate base path. By default, all file paths are
                relative to the Gruntfile. (grunt.file.setBase) *
    --no-color  Disable colored output.
   --gruntfile  Specify an alternate Gruntfile. By default, grunt looks in the
                current or parent directories for the nearest Gruntfile.js or
                Gruntfile.coffee file.
   --debug, -d  Enable debugging mode for tasks that support it.
       --stack  Print a stack trace when exiting with a warning or fatal error.
   --force, -f  A way to force your way past warnings. Want a suggestion? Don't
                use this option, fix your code.
       --tasks  Additional directory paths to scan for task and "extra" files.
                (grunt.loadTasks) *
         --npm  Npm-installed grunt plugins to scan for task and "extra" files.
                (grunt.loadNpmTasks) *
    --no-write  Disable writing files (dry run).
 --verbose, -v  Verbose mode. A lot more information output.
 --version, -V  Print the grunt version. Combine with --verbose for more info.
  --completion  Output shell auto-completion rules. See the grunt-cli
                documentation for more information.

Options marked with * have methods exposed via the grunt API and should instead
be specified inside the Gruntfile wherever possible.

Available tasks
        uglify  Minify files with UglifyJS. *
       default  Alias for "uglify" task.

Tasks run in the order specified. Arguments may be passed to tasks that accept
them by using colons, like "lint:files". Tasks marked with * are "multi tasks"
and will iterate over all sub-targets if no argument is specified.

The list of available tasks may change based on tasks directories or grunt
plugins specified in the Gruntfile or via command-line options.

For more information, see http://gruntjs.com/
```

有两方面是我们需要注意的：

1. Options: grunt支持的命令
2. Available tasks: 当目录可执行的任务

## 4. Grunt常用插件

+ grunt-contrib-uglify：压缩js代码
+ grunt-contrib-concat：合并js文件
+ grunt-contrib-qunit：单元测试
+ grunt-contrib-jshint：js代码检查
+ grunt-contrib-watch：监控文件修改并重新执行注册的任务

### 1). grunt-contrib-uglify：是执行压缩JS代码的任务

具体操作请查看”2.Grunt安装”，

### 2). grunt-contrib-concat：是执行合并文件的任务。

插件安装及更新到配置

```{bash}

~ D:\workspace\javascript\nodejs-grunt>npm install grunt-contrib-concat --save-dev
```

修改Gruntfile.js文件

```{bash}
grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    concat:{
      options: {
        //定义一个字符串插入没个文件之间用于连接输出
        separator: ';'
      },
      dist: {
          src: ['src/*.js'],
          dest: 'build/<%= pkg.name %>.cat.js'
      }
    },
  });
grunt.loadNpmTasks('grunt-contrib-qunit');
grunt.registerTask('default', ['uglify','concat']);
```

在src目录，新增加文件src/sayBye.js

```{bash}
~ vi src/sayBye.js

var sayBye = function(name){
	return "Bye " + name;
}
```

执行concat任务

```{bash}
~ D:\workspace\javascript\nodejs-grunt>grunt concat
Running "concat:dist" (concat) task
File "build/nodejs-grunt.cat.js" created.

Done, without errors.
```

查看生成的文件build/nodejs-grunt.cat.js

```{bash}
~ vi build/nodejs-grunt.cat.js

var sayHello = function(name){
	return "Hello " + name;
};var sayBye = function(name){
	return "Bye " + name;
}
```

两个文件完全的合并。

### 3). grunt-contrib-qunit：是执行QUint单元测试的任务。

插件安装及更新到配置

```{bash}

~ D:\workspace\javascript\nodejs-grunt>npm install grunt-contrib-qunit --save-dev
```

修改Gruntfile.js文件

```{bash}
grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    qunit: {
      files: ['test/*.html']
    }
  });
grunt.loadNpmTasks('grunt-contrib-qunit');
grunt.registerTask('default', ['uglify','concat','qunit']);
```

创建一个test目录，并编写用于测试的qunit.html文件

```{bash}

~ mkdir test
~ vi test/qunit.html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<link rel="stylesheet" href="http://github.com/jquery/qunit/raw/master/qunit/qunit.css" type="text/css" media="screen" />
<script type="text/javascript" src="http://github.com/jquery/qunit/raw/master/qunit/qunit.js"></script>
<script>
test("hello", function() {
ok(true, "world");
});
</script>
</head>
<body>
<h1 id="qunit-header">QUnit example</h1>
<h2 id="qunit-banner"></h2>
<h2 id="qunit-userAgent"></h2>
<ol id="qunit-tests"></ol>
</body>
</html>
```

执行qunit命令

```{bash}
~ D:\workspace\javascript\nodejs-grunt>grunt qunit
Running "qunit:files" (qunit) task
Testing test/qunit.html .OK
>> 1 assertions passed (67ms)

Done, without errors.
```

完成单元测试！！

### 4). grunt-contrib-jshint：是执行代码验证的任务
插件安装及更新到配置

```{bash}
~ D:\workspace\javascript\nodejs-grunt>npm install grunt-contrib-jshint --save-dev
```

修改Gruntfile.js文件

```{bash}
grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    jshint: {
        files: ['gruntfile.js', 'src/*.js', 'build/*.js'],
        options: {
            globals: {
                exports: true
            }
        }
    }
  });
grunt.loadNpmTasks('grunt-contrib-jshint');
grunt.registerTask('default', ['uglify','concat','qunit','jshint']);
```

执行jshint代码检查

```{bash}
~ D:\workspace\javascript\nodejs-grunt>grunt jshint
Running "jshint:files" (jshint) task
Linting src/nodejs-grunt.js ...ERROR
[L3:C2] W033: Missing semicolon.
}
Linting build/nodejs-grunt.cat.js ...ERROR
[L5:C2] W033: Missing semicolon.
}
Linting build/nodejs-grunt.min.js ...ERROR
[L2:C42] W033: Missing semicolon.
var sayHello=function(l){return"Hello "+l};

Warning: Task "jshint:files" failed. Use --force to continue.

Aborted due to warnings.
```

好多的错误啊，细看一下，都是”丢失分号”的错误。

```{bash}
~ vi src/sayBye.js
var sayBye = function(name){
	return "Bye " + name;
};
```

增加最后一行的分号，解决上面的错误。

### 5). grunt-contrib-watch，是监控指定文件被修改，重新启动已注册的任务
我感觉这个插入，就点类似于supervisor的功能。

插件安装及更新到配置

```{bash}
~ D:\workspace\javascript\nodejs-grunt>npm install grunt-contrib-watch --save-dev
```

修改Gruntfile.js文件

```{bash}

grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    watch: {
        files: ['<%= jshint.files %>'],
        tasks: ['jshint', 'qunit']
    }
  });
grunt.loadNpmTasks('grunt-contrib-watch');
grunt.registerTask('default', ['uglify','concat','qunit','jshint']);
```

执行watch任务

```{bash}
~ D:\workspace\javascript\nodejs-grunt>grunt watch
Running "watch" task
Waiting...OK

#手动修改src/sayBye.js文件，下面watch的任务被触发
>> File "src\sayBye.js" changed.

Running "jshint:files" (jshint) task
Linting src/sayBye.js ...ERROR
[L3:C2] W033: Missing semicolon.
}
Linting build/nodejs-grunt.cat.js ...ERROR
[L3:C3] W032: Unnecessary semicolon.
};;var sayBye = function(name){
Linting build/nodejs-grunt.min.js ...ERROR
[L2:C42] W033: Missing semicolon.
var sayHello=function(l){return"Hello "+l};

Warning: Task "jshint:files" failed. Use --force to continue.

Aborted due to warnings.
Completed in 0.770s at Sat Aug 17 2013 20:49:15 GMT+0800 (中国标准时间) - Waiting...
```

上面介绍的5个任务，可能是我们比较常用配置的任务，大家也可以按照需要指定自己的任务。

下面贴上所最终的package.json和Gruntfile.js文件代码

#### package.json

```{bash}

{
  "name": "nodejs-grunt",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "express": "3.2.2",
    "ejs": "*"
  },
  "devDependencies": {
    "grunt": "~0.4.1",
    "grunt-contrib-uglify": "~0.1.1",
    "grunt-contrib-concat": "~0.3.0",
    "grunt-contrib-qunit": "~0.2.2",
    "grunt-contrib-jshint": "~0.6.3",
    "grunt-contrib-watch": "~0.5.2"
  }
}
```

#### Gruntfile.js

```{bash}
module.exports = function(grunt) {

  // Project configuration.
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    uglify: {
      options: {
        banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
      },
      build: {
        src: 'src/<%= pkg.name %>.js',
        dest: 'build/<%= pkg.name %>.min.js'
      }
    },
    concat:{
      options: {
        //定义一个字符串插入没个文件之间用于连接输出
        separator: ';'
      },
      dist: {
          src: ['src/*.js'],
          dest: 'build/<%= pkg.name %>.cat.js'
      }
    },
    qunit: {
      files: ['test/*.html']
    },
    jshint: {
        files: ['gruntfile.js', 'src/*.js', 'build/*.js'],
        options: {
            globals: {
                exports: true
            }
        }
    },
    watch: {
        files: ['<%= jshint.files %>'],
        tasks: ['jshint', 'qunit']
    }
  });

  grunt.loadNpmTasks('grunt-contrib-uglify');
  grunt.loadNpmTasks('grunt-contrib-concat');
  grunt.loadNpmTasks('grunt-contrib-qunit');
  grunt.loadNpmTasks('grunt-contrib-jshint');
  grunt.loadNpmTasks('grunt-contrib-watch');

  // Default task(s).
  grunt.registerTask('default', ['uglify','concat','qunit','jshint']);

};
```

#### 转载请注明出处：http://blog.fens.me/nodejs-grunt-intro/


















