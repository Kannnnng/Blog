---
title: JavaScript 学习笔记（二）prototype、constructor 和 __proto__ 辨析
category: [学习笔记]
tag: [JavaScript,前端开发]
date: 2017-10-3 18:19:06
---

有关于 prototype 这个知识点，我一直以来都是很困惑的，原因大概有两个，一个是入门级的 JavsScript 书籍基本上不会介绍这个知识点，另一个是实际项目中基本上没有用到过 prototype，所以一直不知道它的具体作用到底是什么。但是，现在用不到不代表以后也用不到，而且理解 prototype 对于理解 JavaScript 面向对象编程的思想比较重要，因此还是有必要弄清楚它的作用。我理解这一部分知识是看的这两篇文章，在这里把这两篇文章的链接贴出来。<!--more-->

[JS 中的 prototype](http://www.cnblogs.com/yjf512/archive/2011/06/03/2071914.html#!comments)
[一张图理解 prototype、proto 和 constructor 的三角关系](http://www.cnblogs.com/xiaohuochai/p/5721552.html)

### prototype 以及 constructor 和  \_\_proto\_\_

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
function Foo() {}
var foo = new Foo
console.log(Foo.prototype.constructor === Foo)  // true
console.log(foo.constructor === Foo)  // true
console.log(foo.hasOwnProperty('constructor'))  // false
```

第一个判断，构造函数的 prototype 属性指向了实例对象的原型对象，原型对象有 constructor 属性，其指向了该原型对象的构造函数。在上面例子中，原型对象的构造函数正是 Foo 函数，也就是说实例对象的原型对象的 constructor 属性指向了构造函数本身。

第二个判断，乍一看感觉不正确，因为前面说了，是原型对象而非实例对象拥有 constructor 属性，那为什么上面的判断是 true 呢？这是因为继承的原因，就像我们前面所说的 Java 中的例子，子类可以继承父类中的某些属性和方法，在 JavaScript 中，实例对象同样也可以继承原型对象的某些属性和方法。在上面的代码中，实例对象 foo 继承了原型对象的 constructor 属性，而第一个判断已经说明原型对象的 constructor 属性与 Foo 函数是相等的，因此这第二个判断也是正确的。

第三个判断，hasOwnProperty 方法用于辨识某一个属性是否是对象自己的而非继承而来的，如果属性是对象自己本身拥有的，则返回 true，否则返回 false。很明显，在第二个判断中我们已经说明 constructor 属性不是实例对象 foo 自己拥有的，而是继承自其原型对象，其更确切的表达方式应该是`foo.__proto__.constructor`，所以这里的判断值为 false。

``` javascript
function Foo() {}
var foo = new Foo
console.log(Foo.prototype.__proto__ === Object.prototype)  // true
```

构造函数 Foo 的 prototype 属性指向原型对象，原型对象的 \_\_proto\_\_ 属性指向原型对象的原型对象，这里我们注意到，我们可以先寻找 原型对象的构造函数，找到原型对象的构造函数以后，则原型对象的构造函数的 prototype 属性就指向原型对象的原型对象（有点绕）。那么原型对象的构造函数是什么呢？我们注意到，原型对象本身还是属于对象，**在 JavaScript 中对象都是可以通过`new Object`来初始化的**，那么自然其构造函数即为 Object，因此原型对象的原型对象也就是 Object.prototype，所以上面的判断是正确的。

``` javascript
function Foo() {}
var foo = new Foo
console.log(Foo.prototype.constructor === Foo)  // true
console.log(Object.prototype.constructor === Object)  // true
console.log(Foo.prototype.hasOwnProperty('constructor'))  // true
```

第一个判断，前面已经做了说明，实例对象的原型对象的 constructor 属性指向了构造函数本身，因此是正确的。

第二个判断，根据第一个判断即可得出第二个判断是正确的。另外我们在这里还可以发现，**JavaScript 中所有的对象都可以看做是由构造函数 Object 生成的**。

第三个判断，构造函数的 prototype 属性指向了原型对象，原型对象是拥有 constructor 属性的，因此这个判断也是正确的。

``` javascript
console.log(Object.prototype.__proto__ === null)  // true
```

这个判断可能稍微难以理解一点，首先，`Object.prototype`指向了实例对象的原型对象，那么`Object.prototype.__proto__`则表示以原型对象作为实例对象，该实例对象的原型对象，在上面的判断中，我们了解到**在 JavaScript 中所有的对象都可以看做是 Object 构造函数生成的实例对象**，自然由 Object 构造函数所生成的实例对象都继承了 Object.prototype 所指向的原型对象，**可以认为 Object.prototype 所指向的原型对象已经是 JavaScript 原型链的最顶端，它是固定存在的，而没有继承自其他对象，因此不存在原型对象，它的原型对象就是 null**。

``` javascript
function Foo() {}
var foo = new Foo
console.log(Foo.__proto__ === Function.prototype)  // true
console.log(Object.__proto__ === Function.prototype)  // true
```

在进行判断之前，需要知道的是**在 JavaScript 中函数也是对象，只不过是具有特殊功能的对象而已**，任何函数都可以看做是通过构造函数 Function 通过 new 操作来实例化的结果。看到这里，或许对 JavaScript 中一切皆是对象这句话有了更深的认识。

第一个判断出现了 Function 这个构造函数，如果把 Foo 当成实例对象的话，那么其构造函数就是 Function，函数 Foo 是通过构造函数 Function 的 new 操作来实例化的，原型对象就是 Function.prototype，因此该判断是正确的。

>实际上，代码`function Foo() {}`可以看做`var Foo = new Function()`，构造函数 Function 可以接收任意数量（好像最多是 255 个）的参数，除了最后一个外，前面的所有参数都被看做是要传入实例化以后的函数的参数，最后一个参数被看做函数体，且 Function 接收的所有参数都必须是字符串类型。例如，若要实现一个加法函数，通过构造函数 Function 来初始化的代码即为`var Foo = new Function('num1', 'num2', 'return num1 + num2')`。这种定义函数的方式在实际项目中是不被推荐的，因为上述代码在被执行的时候，第一步是要解析传入构造函数中的字符串，第二部才是基础的 JavaScript 代码解析，这样会导致性能下降，我们的主要目的是为了帮助理解**在 JavaScript 中函数也是通过构造函数生成的实例对象**这个概念。

第二个判断，Object 这个构造函数同样也可以看做是一个实例对象，原型对象就是 Function.prototype，因此该判断也是正确的。

``` javascript
function Foo() {}
var foo = new Foo
console.log(Function.prototype.constructor === Function)  // true
console.log(Foo.constructor === Function)  // true
console.log(Foo.hasOwnProperty('constructor'))  // false
console.log(Object.constructor === Function)  // true
console.log(Object.hasOwnProperty('constructor'))  // false
```

第一个判断，前面已经说过，原型对象的 constructor 属性指向了构造函数本身。

第二个判断，实例对象 Foo 不存在 constructor 属性，但可以通过 \_\_proto\_\_ 属性继承原型对象的 constructor 属性，因此上面的代码可以被改写成`Foo.__proto__.constructor === Function`，因此该判断是正确的。

第三个判断，因为实例对象 Foo 的 constructor 属性是通过 \_\_proto\_\_ 属性继承自原型对象的，所以 hasOwnProperty 的判断结果为 false，因此该判断也是正确的。

第四个判断，根据前面所写，构造函数 Object 同样也可以看做是一个实例对象，其构造函数就是 Function。那么该判断的剩余部分与第二个判断相同，实例对象 Object 通过 \_\_proto\_\_ 属性继承其原型对象的 constructor 属性，因为其原型对象是 Function.prototype，所以 Function.prototype.constructor 也就等于构造函数 Function 本身。

第五个判断，同第三个判断相同，在这里不做赘述。

``` javascript
console.log(Function.__proto__ === Function.prototype)  // true
console.log(Function.prototype.constructor === Function)  // true
```

第一个判断，看上去比较怪异，因为其隐含的意思就是实例对象和构造函数是相同的，在这里就是实例对象 Function 是通过构造函数 Function 的 new 操作初始化的，为什么会有这种关系呢？前面我们讲到，**在 JavaScript 中函数都可以看成是构造函数 Function 的 new 操作初始化的**，因为构造函数 Function 本身也是构造函数，因此它可以看成是通过调用其自身的 new 操作来初始化的结果，那么自然生成的实例对象与构造函数就是相同的了。另外，如果我们执行`console.log(Function.prototype)`这句代码，则结果是`function () {}`，很明显其原型对象就是一个函数体（在 JavaScript 中函数也可看做对象）。

第二个判断，前面已经说过，原型对象的 constructor 属性指向了构造函数本身。

``` javascript
console.log(Function.prototype.__proto__ === Object.prototype)  // true
```

在这里例子中，Function.prototype 同之前所说的一样，是指函数对象的原型对象，并且我们已经知道该原型对象就是 `function () {}`，那么 Function.prototype.__proto__ 即指函数对象的原型对象的原型对象，我们已经知道在 JavaScript 中函数也可以看做是对象，即由构造函数 Object 的 new 操作初始化的，因此 Function.prototype 的原型对象即为 Object.prototype，所以该判断是正确的。
