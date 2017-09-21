---
title: JavaScript 学习笔记（一）问题集合
category: [学习笔记,解决办法]
tag: [JavaScript,前端开发,问题集合]
date: 2017-09-20 11:20:59
---

自从今年三月份末期一直到，大概有半年没有动过博客上面的东西了，一来在公司实习，项目紧，任务重，再加上自己是一个初出茅庐的菜鸟程序员，所以在公司做开发都快忙不过来了，自然也不会有精力和时间更新博客，静下心来学点其他的东西或者巩固一下基础，二来自己也是懒，拖延症晚期患者大概都深有体会，时间总是可以挤出来的，但是就算是好不容易挤出来也都花在了看视频、玩游戏上面了，唉，开局先自我检讨一番……

其实在公司这段时间，虽然没时间学点自己想学的东西，但是收获是真的不小，毕竟一上来就是接触一个已经上线、用户数量将近四十万人的大项目，从最开始看到整个项目文件多到让我一脸懵逼，到后来逐步逐步看懂整个项目的组织结构，改一改样式这样的小操作，到最近独立完成项目规划中一整个功能模块，可以说一路上都在被项目代码碾压过来碾压过去，痛苦与收获并存，眼泪与汗水同流，好在最后没有放弃一路上挺了过来，现在旧的任务已经基本完成，新的任务还没有分配下来，我就趁着这段时间把自己之前将近半年的开发过程中遇到的关于 JavaScript 方面的问题记录在这篇文章中。这篇文章跟我之前的 CSS 学习笔记（三）问题集合应该是差不多的，不会写的很细，但是基本上会把遇到的所有疑问都在这里记录下来，如果有时间的话，我也会一直更新下去的。<!--more-->

### apply、call、bind 之间的联系与区别

首先，这三者的作用都是改变函数执行时的 this 指向的（关于 this 指向，这也是一个很重要的问题，以后会做详细讨论）。它们接收的第一个参数都是 this 要指向的对象，在此之后的所有参数都会被直接传递给要调用的函数。举个例子：

``` javascript
var test = {
  name: 'Tom',
  gender: 'man',
  say: function () {
    console.log(this.name, this.gender)
  },
}

test.say()
```

上面这段代码的运行结果是`Tom man`，很明显当say函数调用的时候，this指向的就是test这个对象。

这个时候我们碰到一个问题，我现在已经知道test对象中有say函数，如果我想在另外的对象上调用say函数怎么办呢？很明显解决办法就是显式地改变say函数调用时this的指向，apply、call、bind 都可以胜任这项工作，下面是各自的解决办法：

``` javascript
var test = {
  name: 'Tom',
  gender: 'man',
  say: function () {
    console.log(this.name, this.gender)
  },
}

var other = {
  name: 'Jerry',
  gender: 'man',
}

test.say.call(other)
test.say.apply(other)

var say = test.say.bind(other)
say()
```

上面代码的执行结果是打印了三个`Jerry man`，其中，apply 和 call 都是直接执行要调用的函数，而 bind 的执行结果是返回了一个经过包装的、this 为指定对象的原函数的副本（不知道具体应该叫什么，不过意思差不多，返回的函数与原函数执行过程相同，仅仅是 this 的只想发生了变化）。

除了上面所说的，三者都接收第一个参数作为 this 的指向，三者还可以接收其他参数，这些参数将被直接传递给调用函数作为它们的参数。下面举个例子：

``` javascript
var test = {
  name: 'Tom',
  gender: 'man',
  say: function (argu1, argu2) {
    console.log(this.name, this.gender, argu1, argu2)
  },
}

test.say('你好', '猫咪')
```

上面代码的执行结果是`Tom man 你好 猫咪`，使用 apply、call、bind 来改变函数的 this 指向的时候，代码形式如下：

``` javascript
var test = {
  name: 'Tom',
  gender: 'man',
  say: function (argu1, argu2) {
    console.log(this.name, this.gender, argu1, argu2)
  },
}

var other = {
  name: 'Jerry',
  gender: 'man',
}

test.say.call(other, '你好', '小老鼠')
test.say.apply(other, ['你好', '小老鼠'])

var say = test.say.bind(other, '你好', '小老鼠')
say()
```

上面代码的执行结果是打印了三个`Jerry man 你好 小老鼠`，所以我们可以看到三者在传递函数的参数这方面的区别是，apply 需要接收一个数组，数组中的元素就是要传递的函数参数，而 call 和 bind 都是将要传递的函数参数直接跟在 this 指向对象的后面。

因此我们可以知道，三者的作用其实是一样的，我们甚至可以用一种方式来实现另外两种方式，下面给出一种 apply 实现 bind 的写法，这也是前端面试中经常遇到的题目。

``` javascript
Function.prototype.bind = function () {
  /* 获取当前函数的 this 指向，也就是其本身 */
  var fn = this
  /* 将传递进来的参数数组复制一个副本，该副本与原参数数组占用两块不同内存空间，但各元素相同 */
  var args = Array.prototype.slice.apply(arguments)
  /* 获取参数数组副本中的第一个参数，也就是 this 要指向的对象，同时将副本数组中的该元素删除 */
  var target = args.shift()
  /* 返回函数 */
  return function () {
    /* apply 用法，第一个参数是 this 要指向的对象，第二个参数是参数数组 */
    return fn.apply(target, args)
  }
}
```
