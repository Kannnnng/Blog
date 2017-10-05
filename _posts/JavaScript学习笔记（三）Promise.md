---
title: JavaScript 学习笔记（三）Promise
category: [学习笔记]
tag: [JavaScript,]
date: 2017-10-3 18:20:52
---

Promise 在 JavaScript 中占有很重要的地位，它在异步编程中作用巨大。在以往的异步编程过程中，回调函数是最为常用的，如果仅仅是简单的回调函数调用，那这种编码方式是可以接受的，但是在实际项目开发过程中，我们经常碰到需要写很多回调函数的情况，我们不得不在回调函数中再次传入回调函数，这样做虽然可以工作，但是代码横向发展而非纵向发展，且回调层级越多，阅读和调试代码越困难。正是在这种情况下，才出现了 Promise 这种异步编程解决方案，当然在最新版本的 ES7 中引入了更加方便的 async/await，但其以 Promise 为基础，因此学习 Promise 成为在新型异步编程中不可或缺的一步。

当然在这里我不会原原本本地将 Promise 的所有知识全部写下来，而是只记录 Promise 比较常用、重要和不易理解的地方，如果读者还没有 Promise 的任何基础知识，推荐阅读阮一峰老师的著作《ES6 标准入门》，在这本书的第十六章 Promise 对象中详细介绍了 Promise 的知识。<!--more-->

### 状态

一个 Promise 对象执行前处于 Pending 状态，以后只有两种可能的状态，一种是代表执行成功的 Resolved 状态，还有一种是代表执行失败的 Rejected 状态，只有异步执行结果才可以决定一个 Promise 对象执行以后应该处于什么状态。

一个 Promise 对象执行完成以后，状态值就不再发生任何变化了，在其执行完成以后的任何时间内都可以获取到该状态，这与事件是不同的，如果事件发生时没有被侦听到，那么后面就再也无法侦听到这个事件了（除非事件再次发生）。例如下面这段代码：

``` javascript
new Promise(function (resolve, reject) {
  setTimeout(function() {
    console.log('1')
    resolve()
  }, 0)

  setTimeout(function() {
    console.log('2')
    reject()
  }, 0)
}).then(function () {
  console.log('3')
}, function () {
  console.log('4')
})
```

上面代码的执行结果是`1 2 3`。

分析上面的结果可知，异步操作调用 resolve 函数或 reject 函数以后，再执行 then 函数传入的两个回调函数参数也是异步的，因为如果是同步的，那么执行结果应该是`1 3 2`；除此之外就是我们上面所说的，一旦 Promise 对象执行完成以后，它的状态是不会再发生变化的，我们可以看到第二个打印后面调用执行 reject 函数以后，并没有再执行第四个打印，原因就是在此之前第一个打印已经调用了 resolve 函数将 Promise 对象的状态转变为 Resolved 状态，其后再调用 reject 函数也不会使 Promise 对象的状态再发生任何变化。。

### 执行时机

一个 Promise 对象被创造（new 操作）以后就会立即执行，至于其什么时候执行完成，则取决于其内部的异步操作何时完成。例如下面这段代码：

``` javascript
console.log('1')

setTimeout(function() {
  console.log('2')
}, 0)

new Promise(function (resolve, reject) {
  setTimeout(function() {
    console.log('3')
    resolve()
  }, 0)

  console.log('4')

  setTimeout(function() {
    console.log('5')
  }, 0)
}).then(function () {
  console.log('6')
}, function () {
  console.log('7')
})

console.log('8')
```

上面代码的执行结果是`1 4 8 2 3 5 6`。

分析上面的结果可知，所有的同步代码是最先执行的，结合我们前面所说的，Promise 对象被创造以后就会立即执行，也就是说其接收的回调函数参数内的代码是同步执行的，因此执行顺序是先执行第一个打印，在执行 Promise 对象内部的第四个打印，最后执行末尾的第八个打印，这样所有的同步代码就全部执行完成了。

执行完同步代码以后再执行等待队列里面的定时器代码，因为三个定时器的延迟时间都是 0 毫秒，所以它们最后的执行顺序取决于被放入等待队列的顺序，最先放入的最先被执行，最后放入的最后被执行，在这里被放入的顺序实际上就是定时器定义的顺序，因此第二个打印、第三个打印和第五个打印依次被执行。

现在就只剩下第六个打印了，我们注意到在执行第三个打印的时候，调用并执行了 resolve 函数，它将 Promise 对象的状态由 Pending 状态转为 Resolved 状态，这个时候就会调用 then 函数传入的两个回调函数参数的第一个回调函数参数，又因为这一步操作是异步的，所以它在最后被执行。

第七个打印没有被执行，因为 Promise 对象内部的异步代码只调用了 resolve 函数而没有调用 reject 函数。

### then 的链式调用

**then 函数返回的还是一个 Promise 对象（不是原来的 Promise 对象）**，而 Promise 对象都是有 then 函数的，其后面还可以调用 then 函数，这样便形成了链式调用。例如下面这段代码：

``` javascript
new Promise(function(resolve, reject) {
  console.log('1')
  resolve(2)
}).then(function (value) {
  console.log(value)
  return 3
}).then(function (value) {
  console.log(value)
}).then(function () {
  console.log('4')
  return Promise.resolve(5)
}).then(function (value) {  // 只定义了 resolve 函数而没有定义 reject 函数
  console.log(value)
  return Promise.reject(6)
}).then(function (value) {  // 定义了 resolve 函数和 reject 函数
  console.log('resolve: '+ value)
}, function(error){
  console.log('reject: ' + error)
})
```

上面代码的执行顺序是`1 2 3 4 5 reject: 6`。这其中 then 函数其实返回了好几种不同的值，但它们最后都被处理成了 Promise 对象。在开始部分，执行`resolve(2)`，则将 2 作为参数传入了 then 函数的第一个回调函数参数，并最终打印出了 2；然后 then 函数返回了数字 3，它被处理成一个 Promise 对象，并作为参数传递给了下一个 then 函数的第一个回调函数参数，并最终打印出了 3；这个时候我们可以看到打印出 3 以后并没有返回语句，但仍然可以继续调用 then 方法，所以这说明在 then 函数中即使不返回任何值，JavaScript 引擎仍然会生成一个新的 Promise 对象作为缺省返回值返回；后面的两个 then 方法分别显式调用了 Promise.resolve 和 Promise.reject 这两个 API 将传入的参数转换成 Promise 对象（只是状态分别为 Resolved 和 Rejected），并分别传入参数作为各自下一个 then 函数接收的两个回调函数参数的参数（好特么的绕）。

### reject 的调用时机

then 函数接收的 reject 函数的调用时机，不仅仅是在 Promise 对象中的异步代码显式调用`reject()`的时候，而且当 Promise 对象中的代码抛出错误的时候也会被执行。例如下面这段代码：

``` javascript
new Promise(function(resolve, reject) {
  console.log('1')
  reject('2')
}).then(function() {}, function(value) {
  console.log(value)
})

new Promise(function(resolve, reject) {
  console.log('1')
  throw new Error('2')
}).then(function() {}, function(value) {
  console.log(value)
})
```

上面代码的执行结果是`1 2 1 2`，可见，不仅仅在执行了`reject()`以后 reject 函数被执行了，而且在 Promise 内部代码抛出错误的时候，reject 函数也被执行了。

### catch 是 then(null, rejection) 的别名

这一点是比较容易被忽略的，不过也可能是我自己一个人忽略了……我以前一直以为 catch 方法和 then 方法是两个独立的方法，但实际上 catch 是 then 的子集。例如下面这段代码中，两种写法在处理错误异常的时候作用是相同的。

``` javascript
new Promise(function(resolve, reject) {
  /* some code */
}).then(function() {
  /* some code */
}, function() {
  /* handle error */
})

new Promise(function(resolve, reject) {
  /* some code */
}).catch(function() {
  /* handle error */
})
```

