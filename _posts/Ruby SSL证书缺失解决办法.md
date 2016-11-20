---
title: Ruby SSL证书缺失解决办法
category: 解决办法
tag: [前端开发]
date: 2016-11-09 16:29:32
---

我这几天在实验室一直做前端开发方向，事情没做多少，就是感觉开发环境各种坑，昨天跟学长去公司配置环境，倒腾了一整天还没有配置完成，一直到今天下午，我睡觉起来发誓一定今天一定要配置好，然后……就配置成功了，果然还是睡觉刚起来的时候精神。<!--more-->

废话不多说，进入正题，在昨天配置环境的时候，就是因为下面这个错误导致sass总是安装失败，后续步骤无法进行。

``` bash
Error fetching https://gems.ruby-china.org/:
    SSL_connect returned=1 errno=0 state=SSLv3 read server certificate B: certificate verify failed (https://gems.ruby-china.org/specs.4.8.gz)
```

意思很明显，就是SSL证书验证不了，Ruby自己没有SSL证书，所以https请求被服务器拒绝。

针对这种情况，有以下两种解决办法。

1.不使用https协议来请求数据，用http协议来代替之，因为http协议不需要验证SSL证书，所以上面那个问题也就不存在了；
2.既然Ruby自己没有携带SSL证书，那我就自己下载一个证书添加给他不就好了，这样Ruby有了SSL证书，https协议也就不会被服务器拒绝了；
<p style="padding-left: 2em">Step1：下载Ruby证书，这里是下载链接: [百度网盘](http://pan.baidu.com/s/1gfzAJKZ) 密码: ithw；
Step2：下载完成以后将证书放置在一个不经常改动的地方，我放置在c:\Program Files\Ruby22\lib下。之后在用户环境变量中新建一个变量，名字为SSL_CERT_FILE，值设置为证书的完整路径，例如我的路径需要设置为c:\Program Files\Ruby22\lib\ca-bundle.crt，完成以后将cmd窗口重启，之后再进行操作就成功了。</p>

### Tips
我在配置过程中遇到几个其他的问题，在这里一并提醒下后来者。

1.Ruby因为防火长城的原因，需要配置成国内的镜像，原本国内的镜像地址是<span>https://</span>ruby.taobao.org，现在已经改为了<span>https://</span>gems.ruby-china.org/，这个问题需要注意下。
2.如果你在安装sass过程中遇到了以下错误
``` bash
ERROR: While executing gem (Errno::EACCES)
    Permission denied @ rb_sysopen......
```
这是因为sass安装权限不够造成的，关掉当前cmd，然后重新以管理员身份运行cmd，再继续操作就好了。

最后把我看到的一篇讲解[如何安装Ruby及其他环境](http://www.cnblogs.com/yyman001/p/install_sass_compass_for_window.html)的文章分享给大家。

以后如果还会碰到什么问题，还会在博客里面记录的，嗯，今天就这些。