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

### prototype 以及 constructor 和  \_\_proto\_\_

有关于 prototype 这个知识点，我一直以来都是很困惑的，原因大概有两个，一个是入门级的 JavsScript 书籍基本上不会介绍这个知识点，另一个是实际项目中基本上没有用到过 prototype，所以一直不知道它的具体作用到底是什么。但是，现在用不到不代表以后也用不到，而且理解 prototype 对于理解 JavaScript 面向对象编程的思想比较重要，因此还是有必要弄清楚它的作用。我理解这一部分知识是看的这两篇文章，在这里把这两篇文章的链接贴出来。

[JS中的prototype](http://www.cnblogs.com/yjf512/archive/2011/06/03/2071914.html#!comments)
[一张图理解prototype、proto和constructor的三角关系](http://www.cnblogs.com/xiaohuochai/p/5721552.html)

首先，我们先通过下面两行代码分别解释一下构造函数和实例对象：

``` javascript
function Foo() {}
var foo = new Foo
```

#### 构造函数

用来初始化新创建的对象的函数就是构造函数，在上面的例子中，Foo 函数被用来初始化通过 new 操作新创建的 foo 对象，因此 Foo 函数就是构造函数。

#### 实例对象

通过构造函数的 new 操作创建的对象就是实例对象，在上面的例子中，foo 对象就是通过 Foo 函数的 new 操作创建的，因此 foo 对象就是一个实例对象。当然，实例对象可以被多次创建，且众多实例对象相互之间并不相等。例如下面的测试：

``` javascript
function Foo() {}
var foo1 = new Foo
var foo2 = new Foo
console.log(foo1 === foo2)  // false
```

#### 原型对象及 prototype

**首先构造函数拥有 prototype 属性，指向实例对象的原型对象。**

**那什么又是原型对象呢？**这其实与 Java 中类的继承很相似，学过 Java 的同学都知道，类是可以有继承关系的，例如我们定义一个父类，它包含有一个方法，这个时候如果我们再定义一个子类，并且让这个子类继承于父类，那么这个子类也就可以调用父类中定义的方法了（当然父类中的方法是 public 类型，具体不细说）。在 JavaScript 中，虽然没有类的明确定义，但是这种父类与子类之间的继承思想还是存在的，例如下面这段代码：

``` javascript
function BaseClass() {
  this.showMsg = function() {
     console.log('BaseClass::showMsg')
  }
}

function ExtendClass() {
}

ExtendClass.prototype = new BaseClass()
var instance = new ExtendClass()
instance.showMsg()  // BaseClass::showMsg
```

在上面的代码中，我们可以类比 Java 中父类与子类的关系，认定 BaseClass 为父类，ExtendClass 为子类，两者既然为继承关系，则子类 ExtendClass 自然也就可以调用父类 baseClass 中的 showMsg 方法，即使该方法在子类 ExtendClass 中没有定义。而实现这个“继承”效果的方法就是通过将 ExtendClass 的 prototype 属性指向 BaseClass。

换用 JavaScript 的话来说就是，ExtendClass 是一个构造函数，它的 prototype 属性指向了一个通过构造函数 BaseClass 的 new 操作来初始化了的实例对象，之后 的话来说就是，ExtendClass 通过 new 操作初始化了一个实例对象 instance，并在这个实例对象 instance 上调用了 showMsg 这个方法，虽然 showMsg 这个方法在 ExtendClass 没有定义，但是却在 BaseClass 中有定义，所以代码执行的时候先查看实例对象 instance 的构造函数 ExtendClass 是否有 showMsg 这个方法的定义，如果有则执行，没有的话再查看构造函数 ExtendClass 的 prototype 属性所指向的通过构造函数 BaseClass 的 new 操作来初始化了的实例对象中是否存在 showMsg 这个方法的定义，如果有则执行，没有的话在继续查看 BaseClass 的 prototype 属性所指向的实例对象，以此类推，一直找到最顶层，这也就实现了具有 JavaScript 特色的“继承”方式。

回过头来我们再看原型对象，我们就可以知道，上面例子中**通过构造函数 BaseClass 的 new 操作来初始化了的实例对象就是所谓的原型对象**，而构造函数 ExtendClass 的 prototype 属性正是指向了这个原型对象。那最开始的那句话其含义应该也就明白了吧。

当然，原型对象并不一定全部都是通过构造函数的 new 操作来得到的，既然是一个对象，则其定义只要符合对象的定义就可以，例如下面这段代码：

``` javascript
var baseInstance = {
  name: '汤姆猫',
  say: function() {
    console.log('喵喵喵')
  }
}

function ExtendClass() {
}

ExtendClass.prototype = baseInstance
var instance = new ExtendClass()
console.log(instance.name)  // 汤姆猫
instance.say()  // 喵喵喵
```

在上面这段代码中，baseInstance 是我们最先定义好了的，而不是通过构造函数的 new 操作生成的，同样可以作为实例对象 instance 的原型对象。

另外我们在前面也说到，实例对象可以被多次创建，且众多实例对象相互之间并不相等，但如果创建这些实例对象的构造函数相同的话，那么它们的原型对象也是相同的，也就是说，**通过同一个构造函数实例化的多个对象具有相同的原型对象**。例如下面这段代码：

``` javascript
var baseInstance = {
  name: '汤姆猫',
  say: function() {
    console.log('喵喵喵')
  }
}

function ExtendClass() {
}

ExtendClass.prototype = baseInstance
var instance1 = new ExtendClass()
var instance2 = new ExtendClass()
console.log(instance1 === instance2)  // false
console.log(instance1.name === instance2.name)  // true
console.log(instance1.say === instance2.say)  // true
```

至于原因也很简单，就是因为这些实例对象的构造函数都是相同的，而构造函数的 prototype 属性所指向的就是各个实例对象的原型对象，自然各个实例对象的原型对象也就是同一个对象了。

#### constructor

原型对象默认有一个 constructor 属性，其指向该原型对象对应的构造函数。

结合 BaseClass、ExtendClass 的例子，我们可以知道，ExtendClass 的 prototype 属性指向了通过构造函数 BaseClass 的 new 操作来初始化了的实例对象，也即是原型对象，这个原型对象默认有一个 constructor 属性，指向了 BaseClass 这个构造函数。例如下面这段代码：

``` javascript
function BaseClass() {
  this.showMsg = function() {
     console.log('BaseClass::showMsg')
  }
}

function ExtendClass() {
}

ExtendClass.prototype = new BaseClass()
console.log(ExtendClass.prototype.constructor)
```

最后一句打印代码打印出来的正是 BaseClass 这个构造函数。推荐大家在自己的浏览器上都尝试一下。

#### \_\_proto\_\_

实例对象有一个 \_\_proto\_\_ 属性，其指向该实例对象对应的原型对象。

到这里就比较好理解了，那上面那段代码作为例子，实例对象 instance 的 \_\_proto\_\_ 属性指向了通过构造函数 BaseClass 的 new 操作来初始化了的实例对象，也就是 instance 的原型对象，那么 instance 的 \_\_proto\_\_ 属性与其构造函数 ExtendClass 的 prototype 属性是相等的也就可以理解了。

``` javascript
function BaseClass() {
  this.showMsg = function() {
     console.log('BaseClass::showMsg')
  }
}

function ExtendClass() {
}

ExtendClass.prototype = new BaseClass()
var instance = new ExtendClass()
console.log(ExtendClass.prototype === instance.__proto__)  // true
```

#### 练习

有了上面的基础知识，我们现在给出几个实例，加深对前面知识的理解。

``` javascript
function Foo() {}
var foo = new Foo
console.log(foo.__proto === Foo.prototype)  // true
```

这个判断条件应该是比较好理解的，因为前面已经说明了，实例对象的 \_\_proto\_\_ 属性与构造函数的 prototype 属性指向了同一个对象，那就是实例对象的原型对象，因此两者是严格相等的。

``` javascript
function Foo() {};
var foo = new Foo;
console.log(Foo.prototype.constructor === Foo);  // true
console.log(foo.constructor === Foo);  // true
console.log(foo.hasOwnProperty('constructor'));  // false
```

第一个判断，构造函数的 prototype 属性指向了实例对象的原型对象，原型对象有 constructor 属性，其指向了该原型对象的构造函数。在上面例子中，原型对象的构造函数正是 Foo 函数，也就是说实例对象的原型对象的 constructor 属性指向了构造函数本身。

第二个判断，乍一看感觉不正确，因为前面说了，是原型对象而非实例对象拥有 constructor 属性，那为什么上面的判断是 true 呢？这是因为继承的原因，就像我们前面所说的 Java 中的例子，子类可以继承父类中的某些属性和方法，在 JavaScript 中，实例对象同样也可以继承原型对象的某些属性和方法。在上面的代码中，实例对象 foo 继承了原型对象的 constructor 属性，而第一个判断已经说明原型对象的 constructor 属性与 Foo 函数是相等的，因此这第二个判断也是正确的。

第三个判断，hasOwnProperty 方法用于辨识某一个属性是否是对象自己的而非继承而来的，如果属性是对象自己本身拥有的，则返回 true，否则返回 false。很明显，在第二个判断中我们已经说明 constructor 属性不是实例对象 foo 自己拥有的，而是继承自其原型对象，其更确切的表达方式应该是`foo.__proto__.constructor`，所以这里的判断值为 false。

``` javascript
function Foo() {};
var foo = new Foo;
console.log(Foo.prototype.__proto__ === Object.prototype);  // true
```

构造函数 Foo 的 prototype 属性指向原型对象，原型对象的 \_\_proto\_\_ 属性指向原型对象的原型对象，这里我们注意到，我们可以先寻找 原型对象的构造函数，找到原型对象的构造函数以后，则原型对象的构造函数的 prototype 属性就指向原型对象的原型对象（有点绕）。那么原型对象的构造函数是什么呢？我们注意到，原型对象本身还是属于对象，在 JavaScript 中对象都是可以通过`new Object`来初始化的，那么自然其构造函数即为 Object，因此原型对象的原型对象也就是 Object.prototype，所以上面的判断是正确的。

``` javascript
function Foo() {};
var foo = new Foo;
console.log(Foo.prototype.constructor === Foo);  // true
console.log(Object.prototype.constructor === Object);  // true
console.log(Foo.prototype.hasOwnProperty('constructor'));  // true
```

第一个判断，前面已经做了说明，实例对象的原型对象的 constructor 属性指向了构造函数本身，因此是正确的。

第二个判断，根据第一个判断即可得出第二个判断是正确的。另外我们在这里还可以发现，JavaScript 中所有的对象都可以看做是由构造函数 Object 生成的。

第三个判断，构造函数的 prototype 属性指向了原型对象，原型对象是拥有 constructor 属性的，因此这个判断也是正确的。

``` javascript
console.log(Object.prototype.__proto__ === null);  // true
```

这个判断可能稍微难以理解一点，首先，`Object.prototype`指向了实例对象的原型对象，那么`Object.prototype.__proto__`则表示以原型对象作为实例对象，该实例对象的原型对象。在上面的判断中，我们了解到在 JavaScript 中所有的对象都可以看做是 Object 构造函数生成的实例对象，那么`Object.prototype`所指向的原型对象也都可以看做是由 Object 构造函数生成的实例对象，这就形成了一种循环式结构，体现到代码上就是`Object.prototype`





















