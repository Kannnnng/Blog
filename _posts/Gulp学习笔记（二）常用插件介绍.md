---
title: Gulp学习笔记（二）常用插件介绍
category: 学习笔记
tag: [Gulp,前端自动化]
date: 2017-03-21 17:09:00
---

在上一篇关于Gulp的学习笔记中，我已经把Gulp的基本使用方法与常用的API写的差不多了，在这一篇文章中，我将主要把自己在学习工作中经常用到的很有帮助的插件介绍一下，Gulp的插件系统为Gulp扩展了丰富的功能，这使得围绕与Gulp的整个生态系统变的非常强大，适当地使用Gulp的插件将会有效提升开发效率与体验。<!--more-->

### stream-combiner2
使用Gulp的过程中，我觉得比较头疼的一件事，就是运行一个Gulp的任务时，如果这中间产生了任何错误或异常，那么这个错误或异常将会被Gulp直接抛出而不做处理，这时整个Gulp任务的运行也就被中断了，然后就需要自己排除完错误代码以后重新启动Gulp任务，这样做其实是比较令人烦恼的。那有没有一个插件，可以在Gulp任务运行中出现错误时，不是直接抛出，而是做出提示，同时整个Gulp任务不会因为错误或异常而被中断？很幸运有这样的插件，这就是stream-combiner2。

通过使用stream-combiner2，你可以将一系列的stream合并成一个，这意味着，你只需要在你的代码中一个地方添加监听器监听发生错误或异常的时间就可以了，同时，stream中发生的任何错误或异常都不会被直接抛出，而是会被监听器捕获，同时整个Gulp任务不会因此而中断。

下面是一个stream-combiner2插件的使用实例。
``` JavaScript
var combiner = require('stream-combiner2');  // 引入stream-combiner2插件
var uglify = require('gulp-uglify');  // 引入js压缩插件
var gulp = require('gulp');  // 引入gulp主模块

gulp.task('test', function() {
  var combined = combiner.obj([
    gulp.src('src/js/**/*.js'),  // 读取js源文件
    uglify(),  // 对js代码进行压缩
    gulp.dest('dist/js')  // 输出压缩以后的js文件
  ]);

  // 任何在上面的 stream 中发生的错误，都不会被抛出，
  // 而是会被监听器捕获
  combined.on('error', console.error.bind(console));

  return combined;
});
```

### gulp-jshint
在了解gulp-jshint之前，先需要了解jshint，jshint是用来检测JavaScript中的语法错误的，它能够帮助你完成js代码的错误排查工作，在代码运行之前发现错误，避免不必要的麻烦，gulp-jshint是使jshint能够在Gulp中正确运行的帮助插件。明白这种依赖关系以后，我们就知道，要想在Gulp中使用jshint帮助检查自己的js代码正确性，不仅需要安装jshint，还需要安装gulp-jshint。

下面是一个gulp-jshint插件的使用实例。
``` JavaScript
var combiner = require('stream-combiner2');  // 引入stream-combiner2插件
var jshint = require("gulp-jshint");  // js检查
var gulp = require('gulp');  // 引入gulp主模块

gulp.task("jshint", function() {
  var combined = combiner.obj([
    gulp.src("src/js/**/*.js"),
    jshint(),
    jshint.reporter("default"),
    gulp.dest("dist/js")
  ]);

  // 任何在上面的 stream 中发生的错误，都不会抛出，
  // 而是会被监听器捕获
  combined.on("error", console.error.bind(console));

  return combined;
});
```
在上面的代码中，gulp-jshint与stream-combiner2结合使用，完成对js代码的错误检查工作。注意到此时js代码的检查规则是jshint默认的检查规则，除此之外你还可以在项目的根目录下新建一个“.jshintrc”文件，在里面使用json语法编写自己定制的js语法检查规则，关于规则定制的详细解释，可以参考[这篇博文](https://my.oschina.net/wjj328938669/blog/637433?p=1)。

### gulp-uglify
这是一款压缩js代码的Gulp插件，在现在的前端项目中，js代码量越来越多，js文件也就越来越大，这样导致客户端在网络环境不太好时加载js代码很缓慢，因此压缩js代码也就变得很有必要。使用这款插件可以有效地压缩js文件大小，例如jQuery库，没有压缩时是290KB，经过压缩以后的文件大小只有89KB，由此可见压缩比还是非常可观的。

下面是一个gulp-uglify插件的使用实例。
``` JavaScript
var combiner = require('stream-combiner2');  // 引入stream-combiner2插件
var uglify = require('gulp-uglify');  // 引入js压缩插件
var gulp = require('gulp');  // 引入gulp主模块

gulp.task('jsmin', function() {
  var combined = combiner.obj([
    gulp.src('src/js/**/*.js'),  // 读取js源文件
    uglify({
      mangle: true,  // 类型：Boolean 默认：true 是否修改变量名
      compress: true,  // 类型：Boolean 默认：true 是否完全压缩
      preserveComments: 'all'  // 保留所有注释
    }),  // 对js代码进行压缩
    gulp.dest('dist/js')  // 输出压缩以后的js文件
  ]);

  // 任何在上面的 stream 中发生的错误，都不会被抛出，
  // 而是会被监听器捕获
  combined.on('error', console.error.bind(console));

  return combined;
});
```
在上面的代码中，gulp-uglify与stream-combiner2结合使用，完成了对js代码的压缩工作。注意到在执行js代码压缩的时候，我们向插件中传入了几个参数，这几个参数的含义在上面都给出了解释，例如选项mangle，其类型为布尔型，含义为是否修改变量名，即把你自己为方便记忆与辨认而设置的名字很长的变量名转换成单字母之类的变量，以此减少代码量，其默认值为true，其实在日常的使用中我们可以忽略这些选项，因为gulp-uglify已经为我们选择了最好的设置，我们直接使用即可。当然，如果你还想了解可以设置的其他选项，你可以参考[这篇博文](https://www.npmjs.com/package/gulp-uglify)。

### gulp-notify
这是一款提示插件，它的完整功能很多，但我在实际使用中基本上就只使用了它的几个功能，就是在完成某一目标时在控制台上打印出提示信息，同时在屏幕右上角或右下角弹出提示框，提示当前某一目标已经完成。

下面是一个gulp-notify插件的简单使用实例。
``` JavaScript
var notify = require("gulp-notify");  // 提示插件
var gulp = require("gulp");  // 引入gulp主模块

gulp.task("html", function() {
  return gulp.src("src/html/**/*.html")
    .pipe(gulp.dest("dist/html"))
    .pipe(notify("html文件输出完成"));
});
```
在上面的代码中，html任务将“src/html”文件夹以及该文件夹下所有的子文件夹内的后缀名为“html”的文件输出到“dist/html”文件夹中，完成后弹出一个提示框，提示“html文件输出完成”，并在控制台上打印出该提示信息。

除此以外，它还可以与gulp-jshint插件一起使用，当gulp-jshint运行出现错误的时候，它可以更好的显示错误信息。下面是一个两者一起使用的简单实例。
``` JavaScript
var notify = require("gulp-notify");  // 提示插件
var jshint = require("gulp-jshint");  // js检查
var gulp = require("gulp");  // 引入gulp主模块

gulp.task('lint', function() {
  gulp.src('src/js/**/*.js')
    .pipe(jshint())
    .pipe(notify(function (file) {
      if (!file.jshint.success) {
        var errors = file.jshint.results.map(function (data) {
          if (data.error) {
            return "(" + data.error.line + ':' + data.error.character + ') ' + data.error.reason;
          }
        }).join("\n");
        return file.relative + " (" + file.jshint.results.length + " errors)\n" + errors;
      }
    }));
});
```
在上面的代码中，gulp-notify作为gulp-jshint的检查结果报告器，在gulp-jshint对js代码检查过程中如果出现错误，则进入gulp-notify插件接收函数里面的if语句中，在这个函数里面，你可以拿到所有js代码检查错误信息，然后你就可以自定义如何显示这些信息，最后将得到的显示结果通过gulp-notify显示出来。

### gulp-rename
这是一款为文件重命名的插件，它可以完成你对文件重命名的很多需求，例如修改文件名，在原文件名的基础上添加前缀与后缀，修改文件的后缀名（扩展名），甚至为文件设置存储路径等。下面是一个gulp-rename插件的使用实例。
``` JavaScript
var combiner = require('stream-combiner2');  // 引入stream-combiner2插件
var uglify = require('gulp-uglify');  // 引入js压缩插件
var rename = require("gulp-rename");  // 重命名
var gulp = require('gulp');  // 引入gulp主模块

gulp.task('jsmin', function() {
  var combined = combiner.obj([
    gulp.src('src/js/**/*.js'),  // 读取js源文件
    uglify(),  // 对js代码进行压缩
    rename({suffix: ".min"}),  // 将文件的
    gulp.dest('dist/js')  // 输出压缩以后的js文件
  ]);

  // 任何在上面的 stream 中发生的错误，都不会被抛出，
  // 而是会被监听器捕获
  combined.on('error', console.error.bind(console));

  return combined;
});
```
在上面的代码中，Gulp首先读取js源文件，之后对js代码进行压缩，然后对压缩好的js文件修改其文件名，即在文件名后面添加“.min”后缀，最后输出文件。这样，一个名为“index.js”的js文件最后输出为名为“index.min.js”的js压缩文件。

除此之外，它还有很多选项可供使用，下面这个示例展示了其他选项的作用。
``` JavaScript
var rename = require("gulp-rename");  // 重命名
var gulp = require('gulp');  // 引入gulp主模块

gulp.src("src/text/hello.txt")  // src/text/hello.txt
  .pipe(rename({
    dirname: "main/markdown",
    basename: "aloha",
    prefix: "bonjour-",
    suffix: "-hola",
    extname: ".md"
  }))
  .pipe(gulp.dest("dist"));  // dist/main/markdown/bonjour-aloha-hola.md
```
在上面的代码中，比较全面的展示了gulp-rename其他选项的作用，其中，“dirname”用于设置文件存储路径，“basename”用于设置文件的基础名，“prefix”为文件名添加前缀，“suffix”为文件名添加后缀，“extname”用于设置文件的扩展名。

### gulp-livereload
这是一款实现代码修改后浏览器自动刷新功能的插件，可以说这个插件帮助我们节省了前端开发过程中一个很费神的事情，使用这款插件以后我们就可以实现仅在编辑器中保存代码，然后浏览器就会自动刷新的效果了。但这款插件的使用相较于上面的几款插件来说就有些复杂，它需要与另外几个插件一起使用才能够达到很好的实时刷新效果。下面是一个gulp-livereload插件与其他几款插件一起使用完成实时刷新功能的使用实例。
``` JavaScript
var livereload = require("gulp-livereload");  // gulp重载插件
var url = require("url");  // url插件
var proxy = require("proxy-middleware");  // 代理插件
var gulp = require('gulp');  // 引入gulp主模块

gulp.task("connect", function() {
  var connect = require("connect");  // 引入connect插件
  var app = connect()
      .use(require("connect-livereload")({ port: 35729 }))  // 引入connect-livereload插件
      .use(require("serve-static")("dist"))  // 引入serve-static插件，并将dist文件夹作为根目录
      .use("/api", proxy(url.parse("http://localhost:4000")));  // 设置向后端发送请求的代理

  require("http").createServer(app)  // 引入http插件
    .listen(9000)
    .on("listening", function() {
      console.log("Started connect web server on http://localhost:9000");
    });
});

gulp.task("serve", ["connect"], function() {
  require("opn")("http://localhost:9000/");  // 引入opn插件，使用系统默认浏览器打开网址“http://localhost:9000”
});

gulp.task("watch", ["serve"], function() {
  livereload({ start: true });  // 启动livereload，开始工作
  gulp.watch(["dist/**"]).on("change", function(file) {  // 监视文件夹“dist”内的所有文件，如果发生变动，则执行后面的回调函数，并将变动的文件信息作为参数传入函数中
    livereload.changed(file.path);
  });
});
```
在上面的代码中，我们前前后后引入了gulp、gulp-livereload、url、proxy-middleware、connect、connect-livereload、serve-static、url、proxy-middleware、http、opn等11个插件，共同完成这个任务，每个插件的作用如下

＋ gulp
＋ gulp-livereload
＋ url
＋ proxy-middleware
＋ connect
＋ connect-livereload
＋ serve-static
＋ url
＋ proxy-middleware
＋ http
＋ opn