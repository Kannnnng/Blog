---
title: JavaScript 学习笔记（三）Promise
category: [学习笔记]
tag: [JavaScript,]
date: 2017-10-3 18:20:52
---

Promise 在 JavaScript 中占有很重要的地位，它在异步编程中作用巨大。在以往的异步编程过程中，回调函数是最为常用的，最常见的编程思想就是，事先制定好某一功能函数，然后通过回调函数的方式使用，当目标事件触发以后，调用事先制定好的功能函数完成某些操作。如果仅仅是简单的回调函数调用，那这种编码方式是可以接受的，但是在实际项目开发过程中，我们经常碰到需要写很多回调函数的情况，我们不得不在回调函数中再次传入回调函数，这样做虽然可以工作，但是代码横向发展而非纵向发展，且回调层级越多，阅读和调试代码越困难。正是在这种情况下，才出现了 Promise 这种异步编程解决方案，因此学习 Promise 在异步编程中将会有很大的帮助。当然在最新版本的 ES7 中引入了更加方便的 async/await，<!--more-->

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

上面这段代码的运行结果是`Tom man`，很明显当 say 函数调用的时候，this 指向的就是 test 这个对象。

这个时候我们碰到一个问题，我现在已经知道 test 对象中有 say 函数，如果我想在另外的对象上调用 say 函数怎么办呢？很明显解决办法就是显式地改变say函数调用时this的指向，apply、call、bind 都可以胜任这项工作，下面是各自的解决办法：

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
