---
title: Gulp学习笔记（二）常用插件介绍
category: 学习笔记
tag: [Gulp,前端自动化]
date: 2017-03-21 17:09:00
---

在上一篇关于Gulp的学习笔记中，我已经把Gulp的基本使用方法与常用的API写的差不多了，在这一篇文章中，我将主要把自己在学习工作中经常用到的很有协助的插件介绍一下，Gulp的插件系统为Gulp扩展了丰富的功能，这使得围绕与Gulp的整个生态系统变的非常强大，适当地使用Gulp的插件将会有效提升开发效率与体验。<!--more-->

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
这是一款实现代码修改后浏览器自动刷新功能的插件，可以说这个插件帮助我们节省了前端开发过程中一个很费神的事情，使用这款插件以后我们就可以实现仅在编辑器中保存代码，然后浏览器就会自动刷新的效果了。下面是一个gulp-livereload插件的使用实例。
``` JavaScript
var gulp = require("gulp");
var sass = require("gulp-sass");
var livereload = require("gulp-livereload");

gulp.task("sass", function() {
  gulp.src("src/sass/**/*.sass")
    .pipe(sass().on('error', sass.logError))
    .pipe(gulp.dest("dist/css"))
    .pipe(livereload());
});

gulp.task('watch', function() {
  livereload.listen();
  gulp.watch("src/sass/**/*.sass", ['sass']);
});
```
在上面的代码中，我们一开始先定义了一个Gulp任务，规定任何sass文件发生变化，就调用任务内部的处理代码将sass编译成css，然后存放到指定位置，注意到在执行完文件输出命令后，还有一句“livereload()”代码需要执行，这一句代码的功能就是在上面的任务执行完成以后自动刷新浏览器，因此如果你希望某一任务（例如js代码编译）完成以后自动刷新浏览器页面，你就需要在相对应的Gulp任务的后面添加这句代码,最后启动“gulp-livereload”侦听功能即可。

<span style="color: red; font-weight: bold">注意：</span>仅使用“gulp-livereload”插件并不能完全实现自动刷新功能，还需要配合chrome插件[LiveReload](https://chrome.google.com/webstore/detail/livereload/jnihajbhpnppcggbcgedagnkighmdlei)，或者在建立本地服务器时使用“connect-livereload”插件，在本文的后面将会讲到“connect-livereload”插件以及如何在搭建本地服务器中使用它。

除了上面这种使用方式，你还可以通过“livereload.changed(path)”来使用“gulp-livereload”，这种方式比上面的更加简单方便，下面是一个通过“livereload.changed(path)”方法来使用“gulp-livereload”的一个实例。
``` JavaScript
var gulp = require("gulp");
var sass = require("gulp-sass");
var livereload = require("gulp-livereload");

gulp.task("sass", function() {
  gulp.src("src/sass/**/*.sass")
    .pipe(sass().on('error', sass.logError))
    .pipe(gulp.dest("dist/css"));
});

gulp.task('watch', function() {
  gulp.watch("src/sass/**/*.sass", ['sass']);
  livereload({ start: true });
  gulp.watch("dist/**").on("change", function(file) {  // 监视文件夹“dist”内的所有文件，如果发生变动，则执行后面的回调函数，并将变动的文件信息作为参数传入函数中
    livereload.changed(file.path);
  });
});
```
在上面的代码中，我们一开始同样先定义了一个Gulp任务，规定任何sass文件发生变化，就调用任务内部的处理代码将sass编译成css，然后存放到指定位置，注意到现在我们不在任务的后面通过添加“livereload()”来指定何时应该刷新浏览器页面，而是放在了后面的“watch”任务中，在“watch”任务里面，首先启动“gulp-livereload”，然后使用Gulp自带的”watch“API来监视位于”dist“文件夹下面的任何文件是否发生变化，如果发生变化，则自动调用后面写好了的回调函数，在回调函数里面，发生变化的文件信息以参数”file“的形式传入函数中，之后通过调用”livereload.changed(path)“来完成相应页面的自动刷新，该API需要传入一个发生变化的文件的路径作为参数，其可以通过”file.path“获得。通过以上工作，我们就实现了”dist“文件夹内的任何文件发生变化时，页面都会根据需要自动刷新的功能，省去了在每一个Gulp任务的后面添加“livereload()”来指定何时刷新的工作。

### proxy-middleware
这是一款在向后端发送请求时设置代理的插件，通俗一点来讲就是更改向后端发送请求的URL，比如你当前页面所属域名为“https:\/\/example.com/endpoint”，那么当你需要向后端发送请求（GET、POST等）时，你请求的URL可能会类似于“https:\/\/example.com/endpoint/getsomething?name=xxx&type=xxx”，这样写不仅麻烦，同时当页面所处域名发生变化时，你就需要改动几乎所有的请求URL，而使用这款插件以后，你就可以在服务器初始化的时候设置当前所处域名，并且之后你的所有请求URL只需要写成“/api/getsomething?name=xxx&type=xxx/”即可。下面是一个“proxy-middleware”插件的使用实例。
``` JavaScript
var connect = require('connect');
var url = require('url');
var proxy = require('proxy-middleware');
 
var app = connect();
app.use('/api', proxy(url.parse('https://example.com/endpoint')));
// now requests to '/api/x/y/z' are proxied to 'https://example.com/endpoint/x/y/z' 
 
//same as example above but also uses a short hand string only parameter 
app.use('/api-string-only', proxy('https://example.com/endpoint'));
```
在上面的例子中，引入了两个我们尚未介绍的插件，其中“connect”插件在此处用于建立一个本地服务器，“url”插件用于将字符串形式的网址转换为URl对象（包含属性有protocol、slashes、auth、host、port、hostname、hash、search、query、pathname、path、href，具体可以通过console.log命令打印查看），在这里我们可以看到，通过使用“proxy-middleware”，向后端发送的请求URl被自动代理到了设置好的域名上，例如“/api/x/y/z”被代理到了“https:\/\/example.com/endpoint/x/y/z”。下面的写法省去了“url”插件，但最后的效果相同，推荐使用这种简单用法。

### opn
这是一款专门用来打开文件、网址和可执行文件的插件，不仅如此，它还可以指定打开文件以后的操作，指定使用什么浏览器来打开网址，下面是一个“opn”插件的使用实例。
``` JavaScript
var opn = require('opn');
 
// 使用系统默认应用打开文件
opn('unicorn.png').then(() => {
  // 这里可以做一些事情
});
 
// 使用系统默认浏览器打开网址
opn('http://www.baidu.com');
 
// 使用指定的浏览器打开网址
opn('http://www.baidu.com', {app: 'firefox'});
 
// 使用指定的浏览器并向浏览器传递一些参数打开网址
opn('http://www.baidu.com', {app: ['google chrome', '--incognito']});
```
在上面的代码中，第一段代码使用“opn”通过系统默认应用打开了一个名叫“unicorn.png”的图片，之后没有进行任何操作；第二段代码使用系统默认的浏览器打开百度首页；第三段代码使用火狐浏览器打开百度首页，第四段代码使用谷歌浏览器在隐身模式下打开了百度首页。在实际工程里面我一般用它在本地服务器搭建好以后通过默认浏览器打开起始网页，然后开始开发工作……

### connect-livereload
这是一款协助"gulp-livereload"插件实现自动刷新页面功能的插件，有了它你就可以不必安装chrome插件，当然，如果你的浏览器现在已经安装了“LiveReload”插件，那你就不再需要这款插件，如果没有安装该插件，你可以选择安装，或者使用“connect-livereload”，下面是一个“connect-livereload”的简单使用实例。
``` JavaScript
var connect = require("connect");
var app = connect()
    .use(require("connect-livereload")({ port: 35729 }));
```
在上面的代码中，引用了我们尚未介绍的一个插件“connect”，这个插件在上面的一个实例中也被用到了，其作用可以简单地理解为建立一个本地服务器，在建立过程中，通过使用“connect”插件的use方法来调用“connect-livereload”插件，这样就可以在不使用chrome扩展的情况下协助“gulp-livereload”插件完成页面的自动刷新功能了。

### 
需要注意的是，上面的例子是假定你已经在本地建立好了服务器（例如通过Wamp建立本地服务器），并能够通过浏览器打开“http:\/\/localhost:8000”页面，如果你尚且没有在本地建立好服务器，那下面这个例子或许能够更好地帮助你，它同时完成了建立服务器、设置服务器目录、设置向后端发送请求时的代理以及页面自动刷新等功能。
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
在上面的代码中，我们前前后后引入了gulp、gulp-livereload、url、proxy-middleware、connect、connect-livereload、serve-static、proxy-middleware、http、opn等10个插件，共同完成这个任务，每个插件的作用如下

＋ gulp
＋ gulp-livereload
＋ url
＋ proxy-middleware
＋ connect
＋ connect-livereload
＋ serve-static
＋ proxy-middleware
＋ http
＋ opn