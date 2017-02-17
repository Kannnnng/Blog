---
title: DOM学习笔记（二）DOM元素
category: 学习笔记
tag: [DOM,前端开发]
date: 2017-02-17 14:36:00
---

DOM元素也是DOM知识中的基础部分，了解DOM元素的特性对以后通过JavaScript控制DOM有很大帮助。<!--more-->

首先，这里的DOM元素暂时指代网页中的各个元素标签，例如div、p、span等；其次，这篇文章同样可能很简洁，不会太深入讨论一些知识点，只是把我学习过程中遇到的比较重要的内容记录下来，以备后用。

### 块级元素、內联元素和内联块级元素
DOM元素按照表现形式划分可以分为块级元素、內联元素和内联块级元素，其CSS属性display分别对应为block、inline和inline-block。

#### 块级元素
1.从左到右撑满页面，独占一行，触碰到页面边缘时，将会自动换行；
2.可以设置width和height属性，但即使是设置了宽度，仍然是独占一行。

常见的块级元素有：div、ul、li、p、form、table、h1～h6……

#### 內联元素
1.能够在同一行内显示，不会产生换行，直到该行填满，才会新换一行；
2.其宽度随内容的多少而变化；
3.不可以设置width和height属性，即使设置也是无效的；
4.可以正常设置padding－left、padding－right、margin－left、margin－right；
5.不能设置margin－top和margin－bottom，即使设置也是无效的；
6.设置padding－top和padding－bottom时，是基于元素内容对齐的，而不是基于边界对齐的，也就是说设置padding－top和padding－bottom时，元素内容位置不变，边框位置发生变化。

常见的內联元素有：span、a、strong、label、select、textarea……

#### 内联块级元素
1.将元素呈递为内联元素，但元素的内容作为块级元素呈递，可以理解为块级元素与內联元素的结合；
2.能够在同一行内显示，不会产生换行，直到该行填满，才会新换一行；
3.能够设置width和height属性。

常见的內联元素有：input、img。

### 常见DOM元素的嵌套规则
这个知识点应该是最常用的，但也是比较容易出问题的，具体看来常见DOM元素的嵌套规则有如下几项：

1.块级元素可以包含內联元素或其他块级元素，但內联元素却不能包含块级元素，它只能包含其他的內联元素。例如：
``` html
<div><h1></h1><p></p></div>    正确
<a href="#"><span></span></a>  正确
<span><div></div></span>       错误
```
2.p元素里面不能放块级元素。这里需要注意的是：<span style="color: red; font-weight: bold">p元素是块级元素，但却不满足第一条规则，所以是第一条规则的特例。</span>例如：
``` html
<p><ol><li></li></ol></p>      错误
<p><div></div></p>             错误
```
3.有几个特殊的块级元素只能包含內联元素，不能再包含块级元素，这几个特殊的标签是：<span style="color: red; font-weight: bold">h1、h2、h3、h4、h5、h6、p、dt。</span>这一条规则是对第二条规则的补充，说明不仅仅只有p元素里面不能放块级元素，h1～h6和dt里面也不能放块级元素。
4.li元素里面可以放置div元素，实际上li元素里面可以放置大多数块级元素和內联元素，甚至是它的父元素ol元素和ul元素。
5.块级元素与块级元素并列，內联元素与內联元素并列。例如：
``` html
<div><h2></h2><p></p></div>    正确
<div><a href="#"></a><span></span></div>  正确
<div><h2></h2><span></span></div>  可以渲染，但不提倡
```