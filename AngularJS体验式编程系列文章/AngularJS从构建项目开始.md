AngularJS从构建项目开始
=========

[AngularJS体验式编程系列文章](http://blog.fens.me/series-angular/)，将介绍如何用angularjs构建一个强大的web前端系统。angularjs是由Google团队开发的一款非常优秀web前端框架。在当前如此多的web框架下，angularjs能脱颖而出，从架构设计上就高人一等，双向数据绑定，依赖注入，指令，MVC，模板。Angular.js创新地把后台技术融入前端开发，扫去jQuery一度的光芒。用angularjs就像写后台代码，更规范，更结构化，更可控。

#### 关于作者

* 张丹(Conan), 程序员Java,R,PHP,Javascript
* weibo：@Conan_Z
* blog: http://blog.fens.me
* email: bsspirit@gmail.com

#### 转载请注明出处：http://blog.fens.me/angularjs-yeoman-project/

![AngularJS从构建项目开始](http://blog.fens.me/wp-content/uploads/2013/08/angularjs-yeoman.png)

#### 前言

随着AngularJS被越来越多的开发人员所了解，AngularJS的应用受到市场的好评。AngularJS是一种新型的以Javascript为基础的框架，以后台的编程思路影响着web前端的开发。在扎根细节之前，让我们先了解AngularJS项目构架。自顶向下的开始，就是不一样的开发。Angular体验式编程从此开始。

#### 目录

1. AngularJS介绍
2. 构建AngularJS项目（Yeoman，angular-seed）
3. AngularJS项目结构（Yeoman）
4. AngularJS必备基础
5. 启动项目

## 1. AngularJS介绍

AngularJS是一个为动态WEB应用设计的结构框架。它能让你使用HTML作为模板语言，通过扩展HTML的语法，让你能更清楚、简洁地构建你的应用组件。它的创新点在于，利用 数据绑定 和 依赖注入，它使你不用再写大量的代码了。这些全都是通过浏览器端的Javascript实现，这也使得它能够完美地和任何服务器端技术结合。

AngularJS介绍，摘自：http://angularjs.cn/A00n

## 2. 构建AngularJS项目（Yeoman，angular-seed）

说到构建项目，目前我了解的有三种：

* 手工项目: 自己建目录，下载类库，写html,js,css，自己实现引用关系
* angular-seed项目: 下载github的angular-seed，在别人的基础之上开发
* Yeoman项目：通过Yeoman下载一个标准Yeoman项目，已经内置了grunt及各种工具包

### 1). 手工项目

构建手工项目是我们平时用的最多的一种方式了，适用于小型或demo项目。我不在解释，参照angularjs一步一步操作就行了。

### 2). angular-seed项目

一般把这个项目称为angular的“种子项目”，构建方法是去github下载项目源代码，基于已有项目结构再做开发。这种项目都融入了前人的经验，会以一种比较合理的结构，帮我们构建出项目的原型。适合于有一定规模项目开发，同时更适合geek的扩展。

下载演示一下构建过程

```{bash}

~ D:\workspace\javascript>git clone https://github.com/bsspirit/angular-seed.git
Cloning into 'angular-seed'...
remote: Counting objects: 1007, done.
remote: Compressing objects: 100% (497/497), done.
emote: Total 1007 (delta 521), reused 847 (delta 412)
Receiving objects: 100% (1007/1007), 6.30 MiB | 164 KiB/s, done.
Resolving deltas: 100% (521/521), done.

~ D:\workspace\javascript>cd angular-seed
~ D:\workspace\javascript\angular-seed>node scripts\web-server.js
Http Server running at http://localhost:8000/
```

打开浏览器：http://localhost:8000/app/index.html

![](http://blog.fens.me/wp-content/uploads/2013/08/angular-seed1.png)

通过阅读READMD.md，我们可以了解这个”种子项目”如何使用。

#### 项目目录及介绍

```{bash}

## Directory Layout
    app/                --> all of the files to be used in production
      css/              --> css files
        app.css         --> default stylesheet
      img/              --> image files
      index.html        --> app layout file (the main html template file of the app)
      index-async.html  --> just like index.html, but loads js files asynchronously
      js/               --> javascript files
        app.js          --> application
        controllers.js  --> application controllers
        directives.js   --> application directives
        filters.js      --> custom angular filters
        services.js     --> custom angular services
      lib/              --> angular and 3rd party javascript libraries
        angular/
          angular.js        --> the latest angular js
          angular.min.js    --> the latest minified angular js
          angular-*.js      --> angular add-on modules
          version.txt       --> version number
      partials/             --> angular view partials (partial html templates)
        partial1.html
        partial2.html

    config/karma.conf.js        --> config file for running unit tests with Karma
    config/karma-e2e.conf.js    --> config file for running e2e tests with Karma

    scripts/            --> handy shell/js/ruby scripts
      e2e-test.sh       --> runs end-to-end tests with Karma (*nix)
      e2e-test.bat      --> runs end-to-end tests with Karma (windows)
      test.bat          --> autotests unit tests with Karma (windows)
      test.sh           --> autotests unit tests with Karma (*nix)
      web-server.js     --> simple development webserver based on node.js

    test/               --> test source files and libraries
      e2e/              -->
        runner.html     --> end-to-end test runner (open in your browser to run)
        scenarios.js    --> end-to-end specs
      lib/
        angular/                --> angular testing libraries
          angular-mocks.js      --> mocks that replace certain angular services in tests
          angular-scenario.js   --> angular's scenario (end-to-end) test runner library
          version.txt           --> version file
      unit/                     --> unit level specs/tests
        controllersSpec.js      --> specs for controllers
        directivessSpec.js      --> specs for directives
        filtersSpec.js          --> specs for filters
        servicesSpec.js         --> specs for services
```

#### 启动server

```{bash}
node scripts/web-server.js
```

**单元测试(Unit test)：**karma + jasmine
**端到端测试(End to End test)：**karma + jasmine + webserver

我们大概了解了“种子工程”的全貌，这样子心理有数了，就可以开始我们的项目开发了。

#### 对于更高要求的开发者来说，“种子工程”的基础是不够。

1. karam，jasmine都需要手动安装
2. 没有代码自动化(自动增加controller…)
3. 没有实现构建自动化(自动打包，自动压缩js…)
……

一个大型项目构成是方方面面的，接下来我们通过标准化的Yeoman来构建一个企业级应用的项目基础。

### 3). Yeoman项目

yeoman是一个标准化的项目开发工作流工具，详细使用介绍请参考：[Yeoman自动构建js项目](http://blog.fens.me/nodejs-yeoman-intro/)

#### 通过yeoman也构建项目

```{bash}
~ D:\workspace\javascript>mkdir nodejs-angular
~ D:\workspace\javascript>cd nodejs-angular
~ D:\workspace\javascript>npm install -g generator-angular

# 创建项目
~ D:\workspace\javascript\nodejs-angular>yo angular
[?] Would you like to include Twitter Bootstrap? Yes
[?] Would you like to use the SCSS version of Twitter Bootstrap with the Compass CSS Authoring Framework? No
[?] Which modules would you like to include? angular-resource.js, angular-cookies.js, angular-sanitize.js
   create app/styles/bootstrap.css
   create app/styles/main.css
   create app\index.html
   create bower.json
   create package.json
   create Gruntfile.js
   invoke   angular:common:D:\toolkit\nodejs\node_modules\generator-angular\app\index.js
   create     .bowerrc
   create     .editorconfig
   create     .gitattributes
   create     .jshintrc
   create     app\.buildignore
   create     app\.htaccess
   create     app\404.html
   create     app\favicon.ico
   create     app\robots.txt
   create     app\views\main.html
   create     test\.jshintrc
   create     test\runner.html
   create     .gitignore
   invoke   angular:main:D:\toolkit\nodejs\node_modules\generator-angular\app\index.js
   create     app\scripts\app.js
   invoke   angular:controller:D:\toolkit\nodejs\node_modules\generator-angular\app\index.js
   create     app\scripts\controllers\main.js
   create     test\spec\controllers\main.js
   invoke   karma:app
   create     karma.conf.js
   create     karma-e2e.conf.js
   create     .travis.yml

I'm all done. Running bower install & npm install for you to install the required dependencies. If this fails, try runni
ng the command yourself.
```

输入yo angular后，会提示我们要不要使用bootstrap;要不要用SCSS生成CSS;要不要include试angular的资源文件。我们选择完以后，会列出生成的项目文件，这个命令执行要2分钟左右，会自动下载很多的依赖包。

## 3. AngularJS项目结构（Yeoman）

![](http://blog.fens.me/wp-content/uploads/2013/08/yo-angular1.png)

#### 更直观地看到目录结构

* .tmp：临时目录
* app:开发的源代码的目录
* dist:生成用于发布的项目
* node_modules:nodejs依赖包
* test:测试文件的目录
* .bowerrc:bower属性
* .editooconfig:对开发工具的属性配置
* .gitattributes:git属性的配置
* .gitignore:git管理文件的配置
* .jshintr:JSHint配置
* .travis.yml:travis-ci持续集成的配置
* bower.json:bower依赖管理
* Gruntfile.js:grunt开发过程管理
* karma.conf.js:karma自动化测试
* karma-e2e.conf.js:karma端到端自动化测试
* package.json:项目依赖文件

## 4. AngularJS必备基础

从上面的目录结构，我们可以看出AngularJS对哪些东西是需要的。

git, yeoman, bower, grunt, karma …

所以，不要着急上手，先把基础工具都掌握，请参考系列文章：[从零开始nodejs系列文章：成为高手 – 进阶篇](http://blog.fens.me/series-nodejs/)

基础知识掌握多少，注定我们能开发多大规模的项目。

## 5. 启动项目

### 我们下载这个工程后，发现没有任何的文档。启动项目应该如何操作呢？

从刚才分析目录及文件结构，我们知道了这个项目是基于grunt构建的，那么一切的操作都会源于Gruntfile.js。grunt的详细介绍，请参考：[grunt让Nodejs规范起来](http://blog.fens.me/nodejs-grunt-intro/)

### 打开Gruntfile.js，直接定位到grunt.registerTask()

```{bash}

  grunt.registerTask('server', function (target) {
    if (target === 'dist') {
      return grunt.task.run(['build', 'open', 'connect:dist:keepalive']);
    }

    grunt.task.run([
      'clean:server',
      'concurrent:server',
      'autoprefixer',
      'connect:livereload',
      'open',
      'watch'
    ]);
  });

  grunt.registerTask('test', [
    'clean:server',
    'concurrent:test',
    'autoprefixer',
    'connect:test',
    'karma'
  ]);

  grunt.registerTask('build', [
    'clean:dist',
    'useminPrepare',
    'concurrent:dist',
    'autoprefixer',
    'concat',
    'copy:dist',
    'cdnify',
    'ngmin',
    'cssmin',
    'uglify',
    'rev',
    'usemin'
  ]);

  grunt.registerTask('default', [
    'jshint',
    'test',
    'build'
  ]);
```

这里定义了4个任务：server，test，build, default。

从名字看就能猜出对应该的功能。

### 启动server

```{bash}
~ D:\workspace\javascript\nodejs-angular>grunt server
Running "server" task

Running "clean:server" (clean) task
Cleaning .tmp...OK

Running "concurrent:server" (concurrent) task

Running "coffee:dist" (coffee) task

Running "copy:styles" (copy) task

Running "autoprefixer:dist" (autoprefixer) task
File ".tmp/styles/bootstrap.css" created.
File ".tmp/styles/main.css" created.

Running "connect:livereload" (connect) task
Started connect web server on localhost:9000.

Running "open:server" (open) task

Running "watch" task
Waiting...
```

浏览器被自动打开：http://localhost:9000/#/

![](http://blog.fens.me/wp-content/uploads/2013/08/yo-angular2.png)

### 执行default任务，生成用于部署的目录dist

```{bash}
~ D:\workspace\javascript\nodejs-angular>grunt --force
Running "jshint:all" (jshint) task
>> 3 files lint free.
Warning: Task "karma" not found. Used --force, continuing.

Running "clean:dist" (clean) task
Cleaning .tmp...OK
Cleaning dist/.htaccess...OK
Cleaning dist/404.html...OK
Cleaning dist/bower_components...OK
Cleaning dist/favicon.ico...OK
Cleaning dist/index.html...OK
Cleaning dist/robots.txt...OK
Cleaning dist/scripts...OK
Cleaning dist/styles...OK
Cleaning dist/views...OK

Running "useminPrepare:html" (useminPrepare) task
Going through app/index.html to update the config
Looking for build script HTML comment blocks

Found a block:
<!-- build:css(.tmp) styles/main.css -->
<link rel="stylesheet" href="styles/bootstrap.css">
<link rel="stylesheet" href="styles/main.css">
<!-- endbuild -->
Updating config with the following assets:
- .tmp\styles\bootstrap.css
- .tmp\styles\main.css

Found a block:
<!-- build:js scripts/plugins.js -->
<script src="bower_components/bootstrap-sass/js/bootstrap-affix.js"></script>
<script src="bower_components/bootstrap-sass/js/bootstrap-alert.js"></script>
<script src="bower_components/bootstrap-sass/js/bootstrap-dropdown.js"></script>
<script src="bower_components/bootstrap-sass/js/bootstrap-tooltip.js"></script>
<script src="bower_components/bootstrap-sass/js/bootstrap-modal.js"></script>
<script src="bower_components/bootstrap-sass/js/bootstrap-transition.js"></script>
<script src="bower_components/bootstrap-sass/js/bootstrap-button.js"></script>
<script src="bower_components/bootstrap-sass/js/bootstrap-popover.js"></script>
<script src="bower_components/bootstrap-sass/js/bootstrap-typeahead.js"></script>
<script src="bower_components/bootstrap-sass/js/bootstrap-carousel.js"></script>
<script src="bower_components/bootstrap-sass/js/bootstrap-scrollspy.js"></script>
<script src="bower_components/bootstrap-sass/js/bootstrap-collapse.js"></script>
<script src="bower_components/bootstrap-sass/js/bootstrap-tab.js"></script>
<!-- endbuild -->
Updating config with the following assets:
- app\bower_components\bootstrap-sass\js\bootstrap-affix.js
- app\bower_components\bootstrap-sass\js\bootstrap-alert.js
- app\bower_components\bootstrap-sass\js\bootstrap-dropdown.js
- app\bower_components\bootstrap-sass\js\bootstrap-tooltip.js
- app\bower_components\bootstrap-sass\js\bootstrap-modal.js
- app\bower_components\bootstrap-sass\js\bootstrap-transition.js
- app\bower_components\bootstrap-sass\js\bootstrap-button.js
- app\bower_components\bootstrap-sass\js\bootstrap-popover.js
- app\bower_components\bootstrap-sass\js\bootstrap-typeahead.js
- app\bower_components\bootstrap-sass\js\bootstrap-carousel.js
- app\bower_components\bootstrap-sass\js\bootstrap-scrollspy.js
- app\bower_components\bootstrap-sass\js\bootstrap-collapse.js
- app\bower_components\bootstrap-sass\js\bootstrap-tab.js

Found a block:
<!-- build:js scripts/modules.js -->
<script src="bower_components/angular-resource/angular-resource.js"></script>
<script src="bower_components/angular-cookies/angular-cookies.js"></script>
<script src="bower_components/angular-sanitize/angular-sanitize.js"></script>
<!-- endbuild -->
Updating config with the following assets:
- app\bower_components\angular-resource\angular-resource.js
- app\bower_components\angular-cookies\angular-cookies.js
- app\bower_components\angular-sanitize\angular-sanitize.js

Found a block:
<!-- build:js({.tmp,app}) scripts/scripts.js -->
<script src="scripts/app.js"></script>
<script src="scripts/controllers/main.js"></script>
<!-- endbuild -->
Updating config with the following assets:
- {.tmp,app}\scripts\app.js
- {.tmp,app}\scripts\controllers\main.js

Configuration is now:

cssmin:
{ 'dist\\styles\\main.css': 'dist\\styles\\main.css' }

concat:
{ 'dist\\styles\\main.css':
[ '.tmp\\styles\\bootstrap.css',
'.tmp\\styles\\main.css' ],
'dist\\scripts\\plugins.js':
[ 'app\\bower_components\\bootstrap-sass\\js\\bootstrap-affix.js',
'app\\bower_components\\bootstrap-sass\\js\\bootstrap-alert.js',
'app\\bower_components\\bootstrap-sass\\js\\bootstrap-dropdown.js',
'app\\bower_components\\bootstrap-sass\\js\\bootstrap-tooltip.js',
'app\\bower_components\\bootstrap-sass\\js\\bootstrap-modal.js',
'app\\bower_components\\bootstrap-sass\\js\\bootstrap-transition.js',
'app\\bower_components\\bootstrap-sass\\js\\bootstrap-button.js',
'app\\bower_components\\bootstrap-sass\\js\\bootstrap-popover.js',
'app\\bower_components\\bootstrap-sass\\js\\bootstrap-typeahead.js',
'app\\bower_components\\bootstrap-sass\\js\\bootstrap-carousel.js',
'app\\bower_components\\bootstrap-sass\\js\\bootstrap-scrollspy.js',
'app\\bower_components\\bootstrap-sass\\js\\bootstrap-collapse.js',
'app\\bower_components\\bootstrap-sass\\js\\bootstrap-tab.js' ],
'dist\\scripts\\modules.js':
[ 'app\\bower_components\\angular-resource\\angular-resource.js',
'app\\bower_components\\angular-cookies\\angular-cookies.js',
'app\\bower_components\\angular-sanitize\\angular-sanitize.js' ],
'dist\\scripts\\scripts.js':
[ '{.tmp,app}\\scripts\\app.js',
'{.tmp,app}\\scripts\\controllers\\main.js' ] }

uglify:
{ dist: { files: { '<%= yeoman.dist %>/scripts/scripts.js': [ 'dist/scripts/scripts.js' ] } },
'dist\\scripts\\plugins.js': 'dist\\scripts\\plugins.js',
'dist\\scripts\\modules.js': 'dist\\scripts\\modules.js',
'dist\\scripts\\scripts.js': 'dist\\scripts\\scripts.js' }

requirejs:
{}

Running "concurrent:dist" (concurrent) task

Running "copy:styles" (copy) task

Running "imagemin:dist" (imagemin) task

Running "coffee:dist" (coffee) task

Running "htmlmin:dist" (htmlmin) task

Running "svgmin:dist" (svgmin) task

Running "autoprefixer:dist" (autoprefixer) task
File ".tmp/styles/bootstrap.css" created.
File ".tmp/styles/main.css" created.

Running "concat:dist\styles\main.css" (concat) task
File "dist\styles\main.css" created.

Running "concat:dist\scripts\plugins.js" (concat) task
File "dist\scripts\plugins.js" created.

Running "concat:dist\scripts\modules.js" (concat) task
File "dist\scripts\modules.js" created.

Running "concat:dist\scripts\scripts.js" (concat) task
File "dist\scripts\scripts.js" created.

Running "copy:dist" (copy) task
Created 63 directories, copied 367 files

Running "cdnify:dist" (cdnify) task
Going through dist/404.html, dist/index.html to update script refs

Running "ngmin:dist" (ngmin) task
ngminifying dist/scripts/modules.js, dist/scripts/plugins.js, dist/scripts/scripts.js

Running "cssmin:dist\styles\main.css" (cssmin) task
File dist\styles\main.css created.

Running "uglify:dist" (uglify) task
File "dist/scripts/scripts.js" created.

Running "uglify:dist\scripts\plugins.js" (uglify) task
File "dist\scripts\plugins.js" created.

Running "uglify:dist\scripts\modules.js" (uglify) task
File "dist\scripts\modules.js" created.

Running "uglify:dist\scripts\scripts.js" (uglify) task
File "dist\scripts\scripts.js" created.

Running "rev:dist" (rev) task
dist/scripts/modules.js >> 6b865daa.modules.js
dist/scripts/plugins.js >> 76c21dca.plugins.js
dist/scripts/scripts.js >> ff635307.scripts.js
dist/styles/main.css >> a5c01db0.main.css

Running "usemin:html" (usemin) task

Processing as HTML - dist/404.html
Update the HTML to reference our concat/min/revved script files
Update the HTML with the new css filenames
Update the HTML with the new img filenames
Update the HTML with data-main tags
Update the HTML with the data tags
Update the HTML with background imgs, case there is some inline style
Update the HTML with anchors images
Update the HTML with reference in input

Processing as HTML - dist/index.html
Update the HTML to reference our concat/min/revved script files
<script src="scripts/plugins.js" changed to <script src="scripts/76c21dca.plugins.js"
<script src="scripts/modules.js" changed to <script src="scripts/6b865daa.modules.js"
<script src="scripts/scripts.js" changed to <script src="scripts/ff635307.scripts.js"
Update the HTML with the new css filenames
<link rel="stylesheet" href="styles/main.css" changed to <link rel="stylesheet" href="styles/a5c01db0.main.css"
Update the HTML with the new img filenames
Update the HTML with data-main tags
Update the HTML with the data tags
Update the HTML with background imgs, case there is some inline style
Update the HTML with anchors images
Update the HTML with reference in input

Processing as HTML - dist/views/main.html
Update the HTML to reference our concat/min/revved script files
Update the HTML with the new css filenames
Update the HTML with the new img filenames
Update the HTML with data-main tags
Update the HTML with the data tags
Update the HTML with background imgs, case there is some inline style
Update the HTML with anchors images
Update the HTML with reference in input

Running "usemin:css" (usemin) task

Processing as CSS - dist/styles/a5c01db0.main.css
Update the CSS with new img filenames

Done, but with warnings.

Elapsed time
jshint:all 69ms
clean:dist 593ms
useminPrepare:html 49ms
concurrent:dist 2s
autoprefixer:dist 65ms
concat:dist\scripts\scripts.js 26ms
copy:dist 475ms
ngmin 21ms
ngmin:dist 210ms
uglify:dist 37ms
uglify:dist\scripts\plugins.js 252ms
uglify:dist\scripts\modules.js 76ms
usemin:html 313ms
usemin:css 82ms
Total 5s
```

有了工具的支持，开发效率就是事半功倍了，前提是你要知道如何善用这些工具！

#### 转载请注明出处：http://blog.fens.me/angularjs-yeoman-project/

