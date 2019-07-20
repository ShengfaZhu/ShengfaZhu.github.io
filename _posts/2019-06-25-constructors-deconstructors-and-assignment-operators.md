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

# Item07. 为多态的基类声明virtual析构函数

举个例子，有多种做法可以记录时间，因此设计一个TimeKeeper base class和一些derived class作为不同的计时方法，是相当合理的，我们一般会用到虚函数多态的技术加上工厂模式实现。

```cpp
class TimeKeeper {
public:
    TimeKeeper();
    ~TimeKeeper();
    ...
};

class AtomicClock : public TimeKeeper {...};
class WaterClock : public TimeKeeper {...};
class WristClock : public TimeKeeper {...};

TimeKeeper* ptk = getTimeKeeper(); // delete ptk or smart pointer
```

上述的实现有个问题，getTimeKeeper()返回的指针指向一个derived class对象，而那个对象却由一个base class指针删除。**当derive 对象经由一个base calss指针删除，而该base class带着一个non-virtual的析构函数，其结果未定义，实际执行通常发生的是对象的derived 成分没有被销毁，造成了诡异的局部销毁对象**。因此，应当为TimeKeeper这种多态基类声明virtual析构函数。

另一方面，若class函数并没有virtual函数，通常表示它并不意图作为一个多态(polymorphic)的基类。此时不应该为其声明virtual析构函数。因为实现virtual函数时，会维护一个虚指针vptr和虚表vtbl，将会无谓增加对象的大小。

最后，当你设计的类不想被继承，应当使用**final**关键字(C++11)。

# Item08. 别让异常逃离析构函数

首先，C++并不禁止析构函数抛出异常，但是析构函数不应当抛出异常。逻辑是这样的，程序运行过程出现了异常，就栈中会存在一些未能正常销毁的局部变量，此时会调用局部变量的析构函数对这些变量进行销毁，析构函数是异常处理的一部分。若析构函数又抛出了异常，前面出现的异常还未处理完成，此时程序出现崩溃或者未定义的行为。

举个例子，一个数据库连接对象，为了确保在使用结束后关闭数据库，在其析构函数中调用了close()。如果调用成功，没有任何问题。但是一旦close()函数抛出了异常，将会造成问题。

```cpp
class DBConn {
public:
    ...
    ~DBConn() {
        db.close();
    }

private:
    DBConnection db;
};
```
两种处理，一是抛出异常，结束程序，调用abort

```cpp
~DBConn() {
    try {db.close();}
    catch (...) {
        log(...);
        std::abort();
    }
}
```
二是吞下异常
```cpp
~DBConn() {
    try {db.close();}
    catch (...) {
        log(...);
    }
}
```
两种方法都可以防止析构函数抛出异常，但是存在较好的策略。首先将调用close()的权利交给用户，如果用户没有使用这项权利，那么再执行上述两种策略之一。

```cpp
class DBConn {
public:
    ...
    void close() {
        db.close();
        closed = true;
    }
    ~DBConn() {
        if (!closed) {
            try {
                db.close();
            }
            catch(...) {
                log(...);
                std::abort() // or not
            }
        }
    }

private:
    DBConnection db;
    bool closed;
};
```

> 《Effective C++》 是本好书.