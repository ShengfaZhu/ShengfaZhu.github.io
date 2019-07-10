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

只要某值保持不变，那么在程序编写的时候就应当明确地指出，以获得编译器的帮助。const具有作用域的概念，可以修饰global或者namespace中的，可以修饰类的成员变量。

## 3.1 const与指针

const出现在*左侧，表示指向的内容是常量；
const出现在*右侧，表示指针本身是常量。

## 3.2 const与迭代器

```cpp
std::vector<int> vec;
const std::vector<int>::iterator iter = vec.begin(); // iter = T* const，指针不能移动
std::vector<int>::const_iterator cIter = vec.begin(); // cIter = const T*, 指向内容不变
```

## 3.3 函数声明中的const

const可以修饰函数返回值、参数和成员函数自身。

### 3.3.1 const修饰函数返回值

如果不返回const变量，编译器将会允许下面的暴行。
```cpp
class Rational {...};

const Rational::operator*(const Ration& lhs, const Ration& rhs);

Rational a, b, c;
(a * b) = c;//在a * b 的结果上调用operator=
```
在许多类中，会提供get方法返回私有变量，此时必须使用const修饰返回值，否则可能会将类的私有成员变量暴露在外面。

### 3.3.2 const修饰成员函数

const修饰成员函数，表示不允许该成员函数修改类的所有成员变量。但是使用mutable修饰的成员变量可以在const函数中修改。

# Item04. 确定对象被使用前已被初始化

读取未初始化的变量的值会导致不确定行为，可能会读出随机的bits。在某些情况下，可能没有问题，但是解决的最好办法是永远在使用对象之前进行初始化。对于内置类型，手工对其进行初始化。对于非内置类型，确保每个构造函数对所有的成员进行了初始化。

**C++规定，对象成员变量的初始化发生在进行构造函数本体前。**

使用初始化列表往往比在构造函数本体中对成员变量进行赋值操作具有更高的效率，因为它仅需要调用一次拷贝构造函数。此外，构造函数列表还可以对const成员进行初始化。

**C++按照初始化列表的次序对变量进行初始化**

在变量的前后声明有关系时，应当注意其在初始化列表中的次序。