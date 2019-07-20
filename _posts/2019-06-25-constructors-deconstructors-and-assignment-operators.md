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

# Item09. 绝不在构造和析构的过程中调用virtual函数

首先，派生类和基类构造、析构函数执行的先后顺序是先执行基类的构造函数，再执行派生类的构造函数；先执行派生类的析构函数，再执行基类的构造函数。

如果在基类的构造函数中调用了virtual函数，本意应当是调用派生类同名的函数，但是此时派生类对象尚未构造，因此不可能也不能（派生类对象尚未构造，不能访问其派生类中增加的成员）调用派生类中的同名函数，只能调用基类中的函数，那么调用virtual函数也没有意义。

如果在析构函数中调用virtual函数，此时派生类对象中增加的成员已被析构，那么只能调用基类中的函数。

因此，在构造函数和析构函数中调用virtual函数，是没有任何意义的。

# Item10. 令operator=返回一个refrence to *this

关于连续赋值：

```cpp
int x, y, z;
x = y = z = 1;
// another way
x = (y = (z = 1));
```

为了实现连续赋值，赋值操作符应当返回一个reference指向操作符左侧的实参，这是应当遵循的**协议**，其他赋值相关的运算符也应当遵循。

```cpp
class Widget {
public:
    Widget& operator=(const Widget& rhs) {
        ...
        return *this;
    }
    Widget& operator+=(const Widget& rhs) {
        ...
        return *this;
    }

};
```

# Item11. 在operator=处理自我赋值

程序设计中总会不经意地进行自我赋值，应当在operator=函数中对其审慎地对待。

```cpp
class Bitmap {...};
class Widget {
    ...
private:
    Bitmap *pb;
};

Widget& Widget::operator=(const Widget &rhs) {
    delete pb;
    pb = new Bitmap(*rhs.pb);
    return *this;
}
```

上述实现的问题在于，如果赋值的目的段和rhs是同一个对象时，先将自己pb的空间释放了，后面也就无法进行访问了。

传统的做法是在前面进行证同测试(identity test)

```cpp
Widget& Widget::operator=(const Widget &rhs) {
    if (this == &rhs) return *this;
    delete pb;
    pb = new Bitmap(*rhs.pb);
    return *this;
}
```

这个做法可以处理自我赋值，但是存在异常安全性的问题。当Bitmap的拷贝构造函数抛出异常时，Widget将始终会有一个指针指向一块Bitmap空间，无法访问也无法释放这个空间。

使用**copy and swap**技术是一个较好的替代方案，前提是编写一个不抛出异常的swap函数。既能保证自我赋值安全，也能保证异常安全性。

```cpp
void Widget::swap(Widget& rhs) {
    ...
}

Widget& Widget::operator=(const Widget &rhs) {
    Widgt tmp(ths)
    swap(tmp);
    return *this;
}
```

# Item12. 复制对象时勿忘其每一个成分

**设计良好的OO-System会将对象的内部进行封装，只留两个函数负责对象的拷贝和赋值，copy构造函数和赋值运算符函数。**

```cpp
class Date {...};
class Customer {
public:
    Customer(const Customer& rhs) : 
        name(rhs.name), lastTransaction(rhs.lastTransaction){}

    Customer& operator=(const Customer& rhs) {
        name = rhs.name;
        lastTransaction = rhs.lastTransaction;
    }
private:
    std::string name;
    Date lastTransaction;
};

class PriorityCustomer : public Customer {
public:
    PriorityCustomer(const PriorityCustomer& rhs) :
        Customer(rhs), priority(rhs.priority) {}

    PriorityCustomer& operator=(const PriorityCustomer& rhs) {
        Customer::operator=(rhs);
        priority = rhs.priority;
    }
private:
    int priority;
}
```

设计良好的拷贝构造函数和赋值运算符应不忘记任何一个成员，如上面的程序所示。应当注意的是，在派生类中，应当显式调用基类的拷贝构造函数和赋值运算符函数，对基类独有的私有成员进行赋值，否则编译器将调用基类中的默认拷贝构造函数，那就很有可能不是希望的结果。

> 《Effective C++》 是本好书.