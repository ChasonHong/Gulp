# Getting Started with Gulp
<p align="center">
  <a href="http://gulpjs.com.cn">
    <img height="257" width="114" src="https://github.com/ChasonHong/Gulp/blob/master/gulp.png">
  </a>
</p>
### Grunt靠边，全新的自动化构建工具来了。

本文假设你之前没有用过任何任务脚本（task runner）和命令行工具，一步步教你上手Gulp。不要怕，它其实很简单，我会分为五步向你介绍gulp并帮助你完成一些惊人的事情。那就直接开始吧。

<h3>第一步：安装Node</h3>
首先，最基本也最重要的是，我们需要搭建node环境。访问
http://nodejs.org
[](http://nodejs.org)
然后点击大大的绿色的“install”按钮，下载完成后直接运行程序(一路next)，就一切准备就绪。[npm](https://www.npmjs.com/)随着安装包一起自动安装，稍后会用到它。

<h3>第二步：使用命令行</h3>
也许现在你还不是很了解什么是命令行（OSX中的终端是Terminal，windows中的是命令提示符Command Prompt），但很快你就会知道。它可能看起来没那么简单，但一旦掌握了它的窍门，就可以很方便的执行很多命令行程序，比如Sass，Yeoman和Git等，这些都是非常有用的工具。

> 如果你很熟悉命令行，那么直接跳到步骤四。

为了确保Node已经正确安装，我们执行几个简单的命令。

```html
node -v
```
回车（Enter），如果正确安装的话，你会看到所安装的Node的版本号。

接下来看看npm。

```html
npm -v
```
同样这应该返回npm的版本号。

如果这两行命令没有得到返回，可能node就没有安装正确，尝试重启下命令行工具，如果还不行的话，只能回到第一步进行重装。

<h3>第三步：定位到项目</h3>
现在，我们已经大致了解了命令行并且知道如何简单使用它，接下来只需要两个简单的命令就能定位到文件目录并看看目录里都有些什么文件。

1. cd，定位到目录
2. ls，列出文件列表

> 建议多花些时间玩这些命令，了解文件系统并知道文件都在哪里

习惯使用了这两个命令后，就要进入我们的项目目录，这对每个人都可能是不同的，举个例子，这是我想为我的本地项目定位的命令：

```html
cd /Applications/XAMPP/xamppfiles/htdocs/my-project
```
成功进入项目目录后，我们开始安装gulp。

<h3>第四步：安装gulp</h3>
我们已经知道如何使用命令行，也知道我们的文件系统，现在尝试点新的东西，认识npm然后安装gulp。

NPM（node package manager）是基于命令行的node包管理器，用于node插件管理（包括安装、卸载、管理依赖等）；它可以将node的程序模块安装到项目中，在它的官网中可以查看和搜索所有可用的程序模块。

在命令行中输入
```html
sudo npm install -g gulp 
```
让我们快速分解这一现象：

1. sudo是以管理员身份执行命令，一般会要求输入电脑密码。
2. npm是安装node模块的工具，执行install命令。
3. -g表示在全局环境安装，以便任何项目都能使用它。
4. 最后，gulp是将要安装的node模块的名字。

运行时注意查看命令行有没有错误信息，安装完成后，你可以使用下面的命令查看gulp的版本号以确保gulp已经被正确安装。

```html
gulp -v
```
接下来，我们需要将gulp安装到项目本地。
```html
npm install --save-dev gulp
```
这里，我们使用--save-dev意思就是，把 Gulp 作为项目的开发依赖，来更新package.json文件，更新devDependencies值，以表明项目需要依赖gulp。

Dependencies可以向其他参与项目的人指明项目在开发环境和生产环境中的node模块依懒关系，想要更加深入的了解它可以看看package.json文档。

现在,gulp安装完成了，下一步是我们要设置gulpfile文件。我们几乎完成了!

<h3>第五步：创建Gulpfile文件和运行gulp</h3>

安装好gulp后我们需要告诉它要为我们执行哪些任务，首先，我们自己需要弄清楚项目需要哪些任务。

* 检查Javascript
* 编译Sass（或Less之类的）文件
* 合并Javascript
* 压缩并重命名合并后的Javascript

<h5>安装插件</h5>

```html
npm install gulp-jshint gulp-sass gulp-concat gulp-uglify gulp-rename --save-dev 
```
> 提醒下，如果以上命令提示权限错误，需要添加sudo再次尝试。

<h5>创建gulpfile文件</h5>
现在，组件都安装完毕，我们需要创建gulpfile文件以指示gulp需要为我们完成什么任务。

gulp只有五个方法： task，run，watch，src，和dest。

在项目根目录创建一个js文件并命名为gulpfile.js，把下面的代码粘贴进去：

<h6>gulpfile.js</h6>
```javascript
// 引入 gulp
var gulp = require('gulp'); 

// 引入组件
var jshint = require('gulp-jshint');
var sass = require('gulp-sass');
var concat = require('gulp-concat');
var uglify = require('gulp-uglify');
var rename = require('gulp-rename');

// 检查脚本
gulp.task('lint', function() {
    gulp.src('./js/*.js')
        .pipe(jshint())
        .pipe(jshint.reporter('default'));
});

// 编译Sass
gulp.task('sass', function() {
    gulp.src('./scss/*.scss')
        .pipe(sass())
        .pipe(gulp.dest('./css'));
});

// 合并，压缩文件
gulp.task('scripts', function() {
    gulp.src('./js/*.js')
        .pipe(concat('all.js'))
        .pipe(gulp.dest('./dist'))
        .pipe(rename('all.min.js'))
        .pipe(uglify())
        .pipe(gulp.dest('./dist'));
});

// 默认任务
gulp.task('default', function(){
    gulp.run('lint', 'sass', 'scripts');

    // 监听文件变化
    gulp.watch('./js/*.js', function(){
        gulp.run('lint', 'sass', 'scripts');
    });
});
```
现在，分段解释下这段代码。

<h5>引入组件</h5>
```javascript
var gulp = require('gulp'); 

var jshint = require('gulp-jshint');
var sass = require('gulp-sass');
var concat = require('gulp-concat');
var uglify = require('gulp-uglify');
var rename = require('gulp-rename');
```

这一步，我们引入了核心的gulp和其他依赖组件，接下来，设置单独的任务，分开创建lint, sass, scripts 和 default这四个不同的任务。

<h5>Lint任务</h5>
```javascript
gulp.task('lint', function() {
    gulp.src('./js/*.js')
        .pipe(jshint())
        .pipe(jshint.reporter('default'));
});
```
Link任务会检查js/目录下得js文件有没有报错或警告。

<h5>Sass任务</h5>
```javascript
gulp.task('sass', function() {
    gulp.src('./scss/*.scss')
        .pipe(sass())
        .pipe(gulp.dest('./css'));
});
```
Sass任务会编译scss/目录下的scss文件，并把编译完成的css文件保存到/css目录中。

<h5>Scripts 任务</h5>
```javascript
gulp.task('scripts', function() {
    gulp.src('./js/*.js')
        .pipe(concat('all.js'))
        .pipe(gulp.dest('./dist'))
        .pipe(rename('all.min.js'))
        .pipe(uglify())
        .pipe(gulp.dest('./dist'));
});
```
scripts任务会合并js/目录下得所有得js文件并输出到dist/目录，然后gulp会重命名、压缩合并的文件，也输出到dist/目录。

<h5>default任务</h5>
```javascript
gulp.task('default', function(){
    gulp.run('lint', 'sass', 'scripts');
    gulp.watch('./js/*.js', function(){
        gulp.run('lint', 'sass', 'scripts');
    });
});
```
这时，我们创建了一个基于其他任务的default任务。使用.run()方法关联和运行我们上面定义的任务，使用.watch()方法去监听指定目录的文件变化，当有文件变化时，会运行回调定义的其他任务。

现在，回到命令行，可以直接运行gulp任务了。
```html
gulp
```
这将执行定义的default任务，换言之，这和以下的命令式同一个意思
```html
gulp default
```
当然，我们可以运行在gulpfile.js中定义的任意任务，比如，现在运行sass任务：
```html
gulp sass
```
<h5>结束语</h5>
现在已经做到了设置gulp任务然后运行他们，现在再回顾下之前学习的。

1. 学习了安装Node环境
2. 学习了简单使用命令行
3. 学习了用命令行进入项目目录
4. 学习了使用npm和安装gulp
5. 学习了如何运行gulp任务

<h6>如果觉得有不合理或者遗漏的地方，请访问<a href="https://github.com/ChasonHong/Gulp/issues/new">这里</a>！</h6>
<h6>另外，有一些参考资源供进一步学习：</h6>

1. [npm上得gulp组件](https://www.npmjs.com/search?q=gulpplugin)
2. [gulp的Github主页](https://github.com/gulpjs/gulp)

本文译自https://travismaynard.com/writing/getting-started-with-gulp

