---
title: jQuery学习笔记（一）问题集合
category: 学习笔记
tag: [jQuery,前端开发]
date: 2016-11-09 16:05:01
---

这篇笔记其实大多都是一些jQuery使用过程中的小常识，但是在系统学习jQuery的时候可能不会碰到，或者仅是看一眼而不会着重去了解，所以就在这里记下来，相当于再温习一遍，加深印象。<!--more-->

### jQuery的HTML元素事件侦听分为静态元素侦听与动态元素侦听
这真是个坑爹的设定，昨天晚上我调试代码，给一个通过jQuery动态生成的Button元素添加鼠标点击侦听事件，但点那个按钮就是死活没反应，开始我以为是我的匹配选择器错了，然后开始改我的选择表达式，甚至一度直接使用ID匹配，结果肯定是没成功，上网找来找去，最后是因为这个原因，令人哭笑不得。所以，以后对元素进行事件绑定或事件侦听的时候，一定要注意分清楚该元素是静态元素还是动态元素，静态元素直接写在HTML文件内，动态元素通过jQuery来动态生成。

那知道了静态元素与动态元素两者之间的区别，实际中对其绑定事件应该怎么做呢？
``` html
<div>
    <button>按钮</button>
</div>
```
静态元素：直接绑定，例如为按钮绑定侦听事件
``` JavaScript
$("button").on("click", function() {...});
$("button").click(function() {...});
```
动态元素：找到要绑定的元素的父元素，且该父元素需要是静态元素，若不是，则找到父元素的父元素，同样需要是静态元素，若不是，继续找下去，直到是为止；之后通过on()来绑定，使用方式是on("事件", "匹配选择器（用来匹配要绑定的元素）", 回调函数)
``` JavaScript
$("div").on("click", "button", function() {...});
```
<span style="color: red; font-weight: bold">注意：</span>与on()作用相同的还有另外一个live()函数，一般在jQuery1.7版本以前使用live()，但是在1.7版本以后live()函数就逐渐被废弃了，新版jQuery推荐全部使用on()函数绑定事件。

### jQuery的触发函数有点小问题
现在有某种需求，点击一个按钮的同时，要求另一个按钮的点击事件也被触发，因此要对第二个按钮模拟点击效果，jQuery提供了一个trigger()函数，使用方法是
``` JavaScript
$("xxx").trigger("事件");
```
但是我在使用过程中发现，使用trigger函数可能会导致一些莫名其妙的错误，比如我使用它来对一个BootStrap的模态框按钮进行模拟点击，模态框会显示出来，但是刚刚显示出来就会马上消失，同时页面仍然处于灰色背景，不能进行其他操作，我不知道这个问题是怎么出现的，但是我换用了JavaScript的原生代码就可以正常工作了。
``` JavaScript
document.getElementById("xxx").click();
```
所以这也让我了解到，使用jQuery过程中出现什么莫名其妙、自己无法解决的问题，换用JavaScript的原生代码是比较好的解决方式。

___
2016.11.09更新：果然，在昨天写代码的过程中再次得到了这个教训，这次是想通过jQuery来对select元素内部的option进行自动选择，使用jQuery需要为被选择的option添加“selected: selected”属性，这样做时好时坏，用调试器看了一下，好像手动选择的option并没有添加上面的属性，最后的效果是，添加了上面的属性能够正常工作，但与手动选择同时使用时就出现问题了，没办法我只好使用JavaScript的原生代码document.getElementById("xxx").selectedIndex，终于解决问题。
___

### typeof的返回值
1.对于数字类型的操作数而言，typeof返回的值是number。比如说typeof(1)，返回的值就是number。

上面是常规数字，对于非常规的数字类型而言，其结果返回的也是number。比如typeof(NaN)，NaN在JavaScript中代表的是特殊非数字值，虽然它本身是一个数字类型；
在JavaScript中，特殊的数字类型还有几种：
``` bash
Infinity                    表示无穷大特殊值
NaN                         特殊的非数字值
Number.MAX_VALUE            可表示的最大数字
Number.MIN_VALUE            可表示的最小数字（与零最接近）
Number.NaN                  特殊的非数字值
Number.POSITIVE_INFINITY    表示正无穷大的特殊值
Number.NEGATIVE_INFINITY    表示负无穷大的特殊值
```
以上特殊类型，在用typeof进行运算，其结果都将是number。

2.对于字符串类型，typeof返回的值是string。比如typeof("123")返回的值是string；
3.对于布尔类型，typeof返回的值是boolean。比如typeof(true)返回的值是boolean；
4.对于对象、数组、null返回的值是object 。比如typeof(window)、typeof(document)、typeof(null)返回的值都是object。<span style="color: red; font-weight: bold">这里面需要特别注意的是数组</span>，我之前以为typeof返回的是array，导致出了不少麻烦；
5.对于函数类型，返回的值是function。比如typeof(eval)、typeof(Date)返回的值都是function；
6.如果运算数是没有定义的（比如说不存在的变量、函数或者undefined），都将返回undefined。比如typeof(sss)、typeof(undefined)都返回undefined。

### 如何获取object成员的属性名
获取一个对象里面各个成员的属性名，可以使用for in循环，例如我现在有下面一个对象
``` JavaScript
course_information = {
    "course_id": 2,
    "course_name": "心理学基础",
    "create_time": "2016-01-28T07:40:11.000Z",
    "student_number": "208",
    "question_number": "14",
    "team_number": "2",
    "signin_number": "21"
}
```
获取该对象中所有成员的属性名，可以编写如下代码
``` JavaScript
for (var key in course_information) {
    console.log(key);
}
```
这里key就是各个成员变量的属性名了。但这里有一个问题，for in循环不仅会遍历对象的自身属性，还会遍历对象的原型属性，而我们可能只关心对象的自身属性，遍历原型属性不仅对我们没有帮助，反而会拖慢代码执行速度，因此我们有必要在使用for in循环时判断一下当前属性是否是对象的自身属性而非原型属性。
``` JavaScript
for (var key in course_information) {
    /* 判断当前属性是否是对象的自身属性而非原型属性 */
    if (course_information.hasOwnProperty(key)) {
        console.log(key);
    }
}
```

### append()函数的注意点
使用jQuery对DOM进行操作的时候，常用的一个函数就是append()函数，它的作用是向DOM树中添加一个DOM元素，一般我们这样使用
``` JavaScript
var $span = $("<span title="新增内容">新增内容</span>");
$("div").append($span);
```
现在有一个需求，向DOM树中新增添的元素比较大，比如一个div容器，里面可能包含众多的子元素，如果还是用上面的写法，那第一行的代码量将会变得很大，影响代码可读性。解决办法之一是先将元素写在HTML文件内，之后设置CSS属性display为none，之后每次需要向DOM树中添加该元素的时候就使用$("div").append($("#xxx"))，既方便又快捷。但实际上这种写法行不通，运行的时候你就会发现，这句代码只有第一次执行的时候有效果，以后再执行就没有反应了，原因在于<span style="color: red; font-weight: bold">将一个已存在的DOM元素通过append()函数添加到DOM树中时，执行的是移动操作而非复制操作</span>，也就是说原来的DOM元素在append()函数执行以后就被删掉了，以后再执行append()函数就找不到原来要添加的DOM元素了，所以更好的解决办法是先将原来的DOM元素克隆一份，然后将克隆以后的副本添加到DOM树中。
``` JavaScript
$("div").append($("#xxx").clone());
```

嗯，暂时想到的就是这么多了，等以后有教训了再写。