---
layout: post
title: Chapter2. 构造、析构和赋值运算
categories: [Reading notes on Effective C++]
description: some word here
keywords: keyword1, keyword2
---
> 《Effective C++》 third edtion, Scott Meyers, 侯捷译.

# Item05. 了解C++默默编写并调用了那些函数
即使是一个空类，C++依旧会**声明**一个default构造函数，析构函数(non virtual)、copy构造函数和赋值运算符。当这些函数被**调用**时，这些函数才会被创建处理。这些默认函数都是**public**成员函数。

- default构造函数：base class和non-static成员变量的构造；
- 析构函数：base class和non-static成员变量的析构；
- copy构造函数：源对象的每个non-static成员变量拷贝至目标对象；
- 赋值运算符：源对象的每个non-static成员变量赋值至目标对象。

# Item06. 若不想使用编译器自动生成的函数，就该明确拒绝
Item05指出即使没有声明，C++也会默默地创建一些函数，如果不想使用编译器所创建的函数，应当明确地指出。

举个例子，任何一个资产都是独一无二的，没有两笔是完全一样的。因此，不能对其进行拷贝和赋值。
```cpp
class HomeForSale {...};

HomeForSale h1;
HomeForSale h2;
HomeForSale h3(h1); // 不该通过编译
h1 = h2; // 不该通过编译
```

有两种方法阻止这一类代码的编译，第一种方法，将希望拒绝的函数声明为**private**, 并且不对其进行实现。这样只有类的member函数和friend函数可以使用这些函数，无法在外部使用这些方法。

```cpp
class HomeForSale {
public:
    ...
private:
    HomeForSale(const HomeForSale&);
    HomeForSale& operator=(const HomeForSale&);
};
```

第二种方法是定义一个专门为了阻止copying动作的base class.为了阻止HomeForSale对象被复制，只需要将HomeForSale继承Uncopyable即可，因为默认生成的函数会尝试调用基类的对应成员函数，而这些成员函数是私有的。以public的方式继承Uncopyable也是可以的，甚至Uncopyable的析构函数不一定是virtual.

```cpp
class Uncopyable {
public:
    Uncopyable () {}
    ~Uncopyable () {}
private:
    Uncopyable(const Uncopyable&);
    Uncopyable& operator=(const Uncopyable&)
};

class HomeForSale : private Uncopyable {
    ...
};
```