---
title: jQuery学习笔记（一）
category: 学习笔记
tag: [jQuery,前端开发]
date: 2016-11-09 16:05:01
---

这篇笔记其实大多都是一些JQ使用过程中的小常识，但是在系统学习jQuery的时候可能不会碰到，或者仅是看一眼而不会着重去了解，所以就在这里记下来，相当于再温习一遍，加深印象。<!--more-->

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
___
<span style="color: red; font-weight: bold">注意：</span>与on()作用相同的还有另外一个live()函数，在jQuery1.7版本以前使用live()，但是在1.8版本以后需要使用on()，否则会提示错误，这点要注意一下。
___
### jQuery的触发函数有点小问题
现在有某种需求，点击一个按钮的同时，要求另一个按钮的点击事件也被触发，因此要对第二个按钮模拟点击效果，jQuery提供了一个trigger()函数，使用方法是
``` JavaScript
$("xxx").trigger("事件");
```
但是我在使用过程中发现，使用trigger函数可能会导致一些莫名其妙的错误，比如我使用它来对一个BootStrap的模态框按钮进行模拟点击，模态框会显示出来，但是刚刚显示出来就会马上消失，同时页面仍然处于灰色背景，不能进行其他操作，我不知道这个问题是怎么出现的，但是我换用了JavaScript的原生代码就可以正常工作了
``` JavaScript
document.getElementById("xxx").click();
```
所以这也让我了解到，使用jQuery过程中出现什么莫名其妙、自己无法解决的问题，使用JavaScript的原生代码是比较好的解决方式。

2016.11.09更新：果然，在昨天写代码的过程中再次得到了这个教训，这次是想通过jQuery来对select元素内部的option进行自动选择，使用jQuery需要为被选择的option添加“selected: selected”属性，这样做时好时坏，用调试器看了一下，好像手动选择的option并没有添加上面的属性，最后的效果是，添加了上面的属性能够正常工作，但与手动选择同时使用时就出现问题了，没办法我只好使用JavaScript的原生代码document.getElementById("xxx").selectedIndex，终于解决问题。

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
在某些情况下我们会需要知道一个对象里面各个成员属性名，例如我现在有下面一个对象
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
我想知道208这个数字对应的属性名是什么，那么我们可以这样写
``` JavaScript
for (item in course_information) {
    if (course_information[item] == "208") {
        console.log(item);
    }
}
```
这里item就是属性名称了。

嗯，暂时想到的就是这么多了，等以后有教训了再写。