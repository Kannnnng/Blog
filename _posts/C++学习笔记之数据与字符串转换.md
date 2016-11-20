---
title: C++学习笔记之数据与字符串转换
category: 学习笔记
tag: [C++,数据类型转换]
date: 2016-10-29 16:17:23
---

在Windows程序代码的调试过程中经常遇到想要将某一变量以特定格式转换成字符串，这个问题可以使用下面的函数来解决。<!--more-->

``` c++
length =  swprintf_s(wchar_t* buffer, const wchar_t* format, [argument,...])
```
1.length，返回值，代表输出字符串长度；
2.buffer，一个宽字符数组指针；
3.format， 输出字符串的内容；
4.argument， 输出字符串参数，可以有多个。

下面是一个使用实例。
``` c++
int x = 0;
int y = 1;
int z = 2;
int length;
wchar_t buffer[1024];
length = swprintf_s(
            buffer,
            L"x = %f\ny = %f\nz = %f\n",
            x,
            y,
            z);
```
与swprintf_s()函数有相同作用的还有sprintf()函数，微软推荐使用前者，因为其具有线程安全特性。其使用方式与前者仅存在一点差异，前者输入的字符指针是宽字符数组指针，后者需要输入的则是普通的字符数组指针。

``` c++
length =  sprintf(char* buffer, const char_t* format, [argument,...])
```

1.length，返回值，代表输出字符串长度；
2.buffer，一个字符数组指针；
3.format，输出字符串内容；
4.argument，输出字符串参数，可以有多个。

下面是一个使用实例。

``` c++
int x = 0;
int y = 1;
int z = 2;
int length;
char buffer[1024];
length = sprintf(
            buffer,
            "x = %f\ny = %f\nz = %f\n",
            x,
            y,
            z);
```