---
layout: post
title: Item01：视C++为一个语言联邦
categories: [Reading notes on Effective C++]
description: some word here
keywords: keyword1, keyword2
---

> 《Effective C++》 third edtion, Scott Meyers, 侯捷译.

C++由C语言发展而来，最早的名字为C with Classes，带类的C++。在C++的发展过程中，进入了很多大胆的尝试，已经突破原先的范畴。目前的C++则是一种**多重泛型编程语言(multiparadigm programming language)**。可以将C++视为一个语言联邦，由不同的子语言构成，在子语言中进行高效编程所需要遵循的准则存在差异。主要有四个子语言:

- C, 语句statement, 预处理preprocessor, 内置数据类型built-in data type, 数组array, 指针pointer;
- Object-oriented C++, class, 封装encapsulation, 继承inheritance, 多态polymorphism;
- Template C++, 泛型编程generic programming;
- STL, 容器container, 迭代器iterators, 算法algorithm, 函数对象function object.
