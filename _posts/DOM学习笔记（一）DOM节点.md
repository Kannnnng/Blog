---
title: DOM 学习笔记（一） DOM 节点
category: 学习笔记
tag: [DOM,前端开发]
date: 2017-02-16 19:42:00
---

DOM 模型存在的意义是为了方便使用 JavaScript 获取或编辑 html 元素，学习好 DOM 对于使用 JavaScript 来控制网页有很大的帮助。DOM 知识在前端开发中属于基础性知识，但想要比较全面的了解还是存在一定难度，写这篇文章的主要目的也是把我在学习过程中遇到的我个人觉得有必要记录下来的 DOM 相关知识收集整理起来，便于日后遇到类似问题时进行查阅，同时也希望能够给看到这篇博客的人一些力所能及的帮助。<!--more-->

这是一篇很简洁的文章，会将一些比较常用又比较重要的 DOM 知识点记录下来，限于时间与精力，暂时不会写的很详细，等以后有时间再回头补充一下。

### DOM 节点类型
DOM 节点类型一共有12种，我们可以使用 nodeType 属性来获得一个节点的节点类型。这里只列出常用的六种：
``` bash
节点类型      节点名称      数值常量     备注

Element      元素节点        1        通常拥有子节点、属性节点和文本节点，也是唯一能够拥有属性节点的节点类型
Attr         属性节点        2        附属于元素节点，并不作为单独的节点在文档中出现
Text         文本节点        3        可以只包含空白和回车，属性的文本内容也属于文本节点
Comment      注释节点        8        注释文本
Document     文档节点        9        不是 html 文档中的根元素，根元素是文档节点的字节点
DocumentType 文档类型节点    10        <!DOCTYPE html>，一般为 html
```
获取方式：
``` javascript
var node = document.getElementById("xxx");
var nodeType = node.nodeType;
```

### DOM 节点名称
不同的 DOM 节点类型对应不同的 DOM 节点名称，我们可以使用 nodeName 属性来获得一个节点的节点名称。
``` bash
节点类型      节点名称       备注

Element      元素的名字     例如：DIV、P、SPAN，注意元素的名字都是大写形式
Attr         属性的名字     例如：id、name、type。注意获取属性节点时需要先获取包含它的元素节点
Text         #text         所有的文本节点，其节点名称都为 #text。注意获取文本节点时需要先获取包含它的元素节点
Comment      #comment      所有的注释节点，其节点名称都为 #comment。注意获取注释节点时需要先获取包含它的元素节点。节点名称获取方式与文本节点相同
DocumentType hmtl          文档类型节点的节点名称一般为 html
```
获取方式：
``` javascript
/* 元素节点 */
var node = document.getElementById("xxx");
var nodeName = node.nodeName;

/* 属性节点 */
var node = document.getElementById("xxx");
var attrNode = node.attributes[0];  // 因为一个元素节点可能包含有多个属性值，所以对应的属性节点是数组形式
var attrName = attrNode.nodeName;

/* 文本节点 */
var node = document.getElementById("xxx");
var textNode = node.childNodes[0];  // 文本节点作为元素节点的子节点，通过子节点数组获取
var attrName = textNode.nodeName;

/* 文档类型节点 */
var nodeName = document.doctype.nodeName;
```

### DOM 节点值
不同的 DOM 节点类型对应不同的 DOM 节点值，我们可以使用 nodeValue 属性来获得一个节点的节点值。
``` bash
节点类型      节点值      备注

Element      null       所有元素节点的节点值都为 null
Attr         属性的值    注意获取属性节点时需要先获取包含它的元素节点
Text         文本内容    注意获取文本节点时需要先获取包含它的元素节点
Comment      注释内容    注意获取文本节点时需要先获取包含它的元素节点。节点值获取方式与文本节点相同
```
获取方式：
``` javascript
/* 元素节点 */
var node = document.getElementById("xxx");
var nodeValue = node.nodeValue;

/* 属性节点 */
var node = document.getElementById("xxx");
var attrNode = node.attributes[0];  // 因为一个元素节点可能包含有多个属性值，所以对应的属性节点是数组形式
var nodeValue = attrNode.nodeValue;

/* 文本节点 */
var node = document.getElementById("xxx");
var textNode = node.childNodes[0];  // 文本节点作为元素节点的子节点，通过子节点数组获取
var attrValue = textNode.nodeValue;

/* 文档类型节点 */
var nodeValue = document.doctype.nodeValue;
```