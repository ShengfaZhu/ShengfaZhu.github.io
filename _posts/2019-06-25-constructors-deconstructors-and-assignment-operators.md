---
layout: post
title: Chapter2. 构造、析构和赋值运算
categories: [Reading notes on Effective C++]
description: some word here
keywords: keyword1, keyword2
---
> 《Effective C++》 third edtion, Scott Meyers, 侯捷译.

# Item05. 了解C++默默编写并调用了那些函数
即使是一个空类，C++依旧会**声明**一个default构造函数，析构函数(non virtual)、copy构造函数和赋值运算符。当这些函数被**调用**时，这些函数才会被创建处理。

- default构造函数：base class和non-static成员变量的构造；
- 析构函数：base class和non-static成员变量的析构；
- copy构造函数：源对象的每个non-static成员变量拷贝至目标对象；
- 赋值运算符：源对象的每个non-static成员变量赋值至目标对象。
