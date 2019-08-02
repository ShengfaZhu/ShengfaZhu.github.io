---
layout: post
title: Chapter3. 资源管理
categories: [Reading notes on Effective C++]
description: some word here
keywords: keyword1, keyword2
---
> 《Effective C++》 third edtion, Scott Meyers, 侯捷译.

所谓资源就是一旦拥有，就需要及时地归还。最为常用的资源是动态内存分配，一旦没有归还它，就会造成资源泄露。

# Item13. 以对象管理资源

```cpp
class Investment {...};

Investment* creatInvestment(); // 返回指针，工厂模式

void f() {
    Investment* pInv = creatInvestment();
    ...
    delete pInv;
}
```

正常来说，当程序从系统申请动态内存（栈区）之后，需要进行delete操作，将动态内存释放。但是当在释放之前出现了过早的return或者异常抛出，则不会正常地释放pInv指向的那片内存。

将资源放在对象中进行管理，可以依赖**c++的析构函数自动调用机制确保资源被释放**。

```cpp
void f() {
    std::shared_ptr<Inverstment> pInv(creatInvestment());
    ...
}
```

上面的例子展示了以对象管理资源两个关键想法：
- **获得资源后立即放入管理对象内**，上面代码以返回的raw pointer来初始化智能指针，是“资源取得时机便是初始化时机(Resource Acquisition Is Initialization, RAII)”，资源在构造函数中获得，在析构函数中释放。
- **管理对象运用析构函数确保资源被释放**

应当指出，智能指针的析构函数内做的是delete动作，而不是delete[]，因此不应像下面一样在动态分配的数组上使用智能指针。

```cpp
std::shared_ptr<int> spi(new int[1024]);// 会用上错误形式的delete
```

std提供的vector已经可以取代动态内存数组的需求，所有尽量不要使用动态内存数组，而是std::vector<>。

# Item14. 在资源管理类中小心coping行为

heap-based的资源可以采用智能指针进行管理，但是并非是所有的资源都是heap-based的，例如互斥对象(Mutex object)，需要在程序结束时，为对象解锁。例如使用C API函数出来互斥对象。

```cpp
void lock(Mutex* pm); // 锁定pm所指的互斥器
void unlock(Mutex* pm); // 解锁pm所指的互斥器

class Lock {
public:
    explicit Lock(Mutex* pm) : mutexPtr(pm) {
        lock(mutexPtr); // 获得资源
    }

    ~Lock() {
        unlock(mutexPtr); // 释放资源
    }

private:
    Mutex *mutexPtr;
};
```

客户对Lock的用法如下所示，满足在“在构造时获得资源，在析构时释放资源”的原则。

```cpp
Mutex m;
{
    Lock ml(&m);
}
```

但是，当RAII对象被复制时，应当审慎地对待。一般有两种处理方式：禁止复制和采用引用计数。

禁止复制在该书的Item06中讨论过，有两种实现方式，一种是将拷贝构造函数和赋值函数定义为私有成员，第二种是将该类私有继承Uncopyable基类。

对底层资源进行引用计数（reference count）。资源可以同时被多个所有者拥有，当资源的最后一个使用者被销毁时，资源释放。可以采用std::shared_ptr进行实现。

```cpp
class Lock {
public:
    explicit Lock(Mutex* pm) : mutexPtr(pm, unlock) // 以unlock()函数为删除器
    {
        lock(mutexPtr.get()); //
    }

private:
    std::shared_ptr<Mutex> mutexPtr;
};
```

析构函数会自动调用non-static成员的析构函数，而MutexPtr的析构函数会在互斥器的计数为0时，调用删除器（unlock），这样也并不需要定义析构函数。

# Item15. 在资源管理类中提供对原始资源的访问

在完美的世界里，采用资源管理类（智能指针）来对抗资源泄露，是一种很好的做法。但是，现实世界中需要用到大量的C API(例如qpOASES等第三方库)，此时不得不直接处理raw resource。因此，需要在资源管理类中提供对原始资源的访问方式。有显式和隐式两种访问的设计方式。

智能指针都提供了get()方法，来进行显式转换，返回智能指针内部的原始指针。

隐式的访问方式是在资源管理类中定义函数运算符操作符重载函数，如下程序所示。

```cpp
class Font{
public:
    ...
    const FontHandle operator() const {
        return f;
    } 

private:
    FontHandle f;
};

```

在调用的时候，编译器会自动调用函数运算符重载函数进行隐式转换。

```cpp
void changeFontSize(FontHandle f, int new_size);

Font f;
int newFontSize;
changeFontSize(f, newFontSize); // Font -> FontHandle的隐式转换
```

隐式转换使得接口使用时更加自然，但是可能会在不经意间进行隐式转换，得到出乎意料的效果；显式转换不容易发生歧义，但是接口使用则显得较为生硬。究竟是提供显式转换还是隐式转换需要权衡。

# Item16. 成对使用new和delete时要采取相同的形式

在C++中，为数组动态申请的空间和为单个变量动态申请的空间，在内存的物理布置上是不同的（数组的动态空间必须要有属性说明数组的长度吧），因此new和delete的形式必须匹配，两者不匹配将导致未定义行为。

```cpp
std::string* stringPtr1 = new std::string;
std::string* stringPtr2 = new std::string[100];

delete stringPtr1;
delete[] strinPtr2;

delete[] stringPtr1; // 未定义行为
delete stringPtr2; // 未定义行为
```

# Item17. 以独立语句将newed对象置入智能指针

这个条目描述的是一种非常特殊的情况。考虑如下的代码：

```cpp
int priority();
void processWidget(std::shared_ptr<Widget> pw, int priority)

processWidget(new Widget, priority()); // 不能通过编译，因为shared_ptr的构造函数的explicit不同进行隐式转换

processWidget(std::shared_ptr<Widget>(new Widget), priority()); // 在特殊情况下，可能发生资源泄露
```

上述程序段的最后一句在极特殊的情况下可能发生资源泄露。上述语句可以分成三件事情：

- 执行new Widget
- 调用shared_ptr的构造函数
- 调用priority()的函数

但是C++编译器有权利在一条语句内部，决定上述三件事情的执行顺序（非常神奇，不是按照参数的顺序进行的），如果按下面的顺序进行，并且调用priority()函数时出现了异常，此时new Widget申请的空间并没有放入智能指针中进行管理，因此会发生资源泄露。

- 执行new Widget
- 调用priority()的函数
- 调用shared_ptr的构造函数

虽然，上述情况的可能性很小，但一但发生就很难察觉。以独立语句将new生产的对象放在智能指针中可以完全杜绝这种情况发生，如下程序所示。保证new申请对象放入资源管理对象（智能指针）在同一条独立语句中，这样中间就不会出现异常了。

```cpp
std::shared_ptr<Widget> pw(new Widget);
processWidget(pw, priority());
```

> 《Effective C++》 是本好书.