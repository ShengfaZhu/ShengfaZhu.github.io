---
layout: post
title: Chapter1. 让自己习惯C++
categories: [Reading notes on Effective C++]
description: some word here
keywords: keyword1, keyword2
---
> 《Effective C++》 third edtion, Scott Meyers, 侯捷译.

# Item01. 视C++为一个语言联邦
C++由C语言发展而来，最早的名字为C with Classes，带类的C++。在C++的发展过程中，进行了很多大胆的尝试，已经大为突破原先的范畴。目前的C++则是一种**多重泛型编程语言(multiparadigm programming language)**。可以将C++视为一个语言联邦，由不同的子语言构成，在子语言中进行高效编程所需要遵循的准则存在差异。主要有四个子语言:

- C, 语句statement, 预处理preprocessor, 内置数据类型built-in data type, 数组array, 指针pointer;
- Object-oriented C++, class, 封装encapsulation, 继承inheritance, 多态polymorphism;
- Template C++, 泛型编程generic programming;
- STL, 容器container, 迭代器iterators, 算法algorithm, 函数对象function object.

# Item02. 尽量以const, enum, inline替换#define


应当指出#define是一种预处理，在编译器开始处理源码之前，预处理器就会将ASPECT_RATIO移走，将其全部替代为1.653
- 如过有与之相关的保错，错误信息将不会出现任何ASPECT_RATIO，define还可能会出现意外的错误
- 如果ASPECT_RATIO出现了多次，则1.653也会出现很多次，可能会比const定义的方式更多的码。
```cpp
#define ASPECT_RATIO 1.653

const double Aspect_ratio = 1.653;
```
## 2.1 用const替代#define

在定义一般的常数量的时候，完全可以用const代替#define，有两种特殊的情况，需要处理。

当定义**常量指针**时，需要用两个const声明指针和指向内容均为常量。
```cpp
const char* const authorName = "Scott Meyers";
```

当定义**类的专属常量**时，需要将常量的作用域限定在类内，并且保证该常量只有一份实体。需要定为static成员，并加上const。#define是没有作用域的概念的，当然也就不能提供任何封装性。
```cpp
class GamePlayer {
private:
    static const int NumTurns = 5;
    int score[NumTurns];
};
```
## 2.2 用enum替代#define

在某些特殊的时候，需要定义一个真正意义的常量，而不是const那种，可以采用"enum hack"的技术。enum hack从行为上更像define而不是const，取一个const的地址的合法的，而去一个enum和define地址是不合法的。

```cpp
class GamePlayer {
private:
    enum {NumTurns = 5};
    int score[NumTurns];
};
```

## 2.3 用inline替代#define

预处理器可以实现像函数样的宏，这种写法应当为所有的变量加上小括号，即使加上小括号，有时也可能出现意外。

```cpp
// 以a,b较大者调用f
#define CALL_WITH_MAX(a, b) f((a)> (b) ? (a) : (b)) 
```
可以用内联函数(inline)代替#define，甚至可以写出class内private的内联函数，保证封装性。
```cpp
template<typename T>
inline void callWithMax(const T& a, const T& b) {
    f(a > b ? a : b);
}
```

# Item03. 尽可能使用const

# Item04. 确定对象被使用前已被初始化


