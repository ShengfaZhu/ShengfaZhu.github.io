---
layout: post
title: Chapter4. 设计与声明
categories: [Reading notes on Effective C++]
description: some word here
keywords: keyword1, keyword2
---
> 《Effective C++》 third edtion, Scott Meyers, 侯捷译.

所谓软件设计，是“令软件作出你希望他做的事情”的步骤和方法。

# Item18. 让接口容易被正确使用，不易被误用

> 理想上来说，如果客户企图以不正确的方式使用接口，这个代码就不应当通过编译。

```cpp
/* bad design */
class Date {
public:
    Date(int mouth, int day, int year);
    ...
};
```

上述代码存在两个问题：

1. 可能传递的年月日三方面的参数不匹配；
2. 可能传递不合法的月份或者年份

```cpp
/* wrapper types */
struct Day{
    explicit Day(int d) : val(d) {}
    int val;
};

struct Month{
    explicit Month(int m) : val(m) {}
    int val;
};

struct Year{
    explicit Year(int y) : val(y) {}
    int val;
};

class Date{
public:
    Date(const Month& m, const Day& d, const Year& y);
    ...
};

Date d(30, 3, 1995); // fail to compile
Date d(Day(30), Month(3), Year(1995)); // fail to compile
Date d(Month(3), Day(30), Year(1995));
```

明智而审慎地导入新类型，可以有效预防接口被误用。

下面再解决如何限制输入的值，

一年的年份只有12个月，所有**Month**也应当反映这件事情。办法之一是使用枚举类型，但是枚举类型并不拥有类型安全性，**enum**总是可以被隐式转化为**int**。比较安全的解法是预先定义所有有效的Month。

``` c++
class Month {
public:
    static Month Jan() {return Month(1);}
    static Month Feb() {return Month(2);}
    ...
       
private:
    explicit Month(int m);
    ...
};

Date d(Month::Jan(), Day(30), Year(1995));
```

- 使用静态成员函数

> 除非有好的理由，否则应该尽量保证定义的类型与内置的类型一致，STL容器的接口就十分的一致

# Item19. 设计class犹如设计type

C++作为一个OOP语言，当定义一个class就是定义一个type，应当带着“语言设计者当初设计语言内置类型时”一样的谨慎来研讨class的设计。

- 新的type如何被创建和销毁(operator new, ner[], delete, delete[])
- 对象的初始化和赋值有什么区别(constructor function and assignment operator)
- 新的type如果被passed by value，意味着什么(copy constructor)
- 什么是新type的合法值
- 新的type需要配置继承图系(inheritance graph)
- 新的type需要怎样的类型转换
- 什么样的操作符和函数对此新type是合理的
- 什么样的标准函数应该被驳回(private or delete)
- 什么是新类型的未声明接口(undeclared interface)
- 新类型有多么一般化，是否需要定义为模板类型
- 真的需要一个新类型吗？是否可以通过现有类型的继承得到

# Item20. 宁以pass by reference to const 替换pass by value

引用传递相较于值传递的优点有两方面：（引用底层的实现是指针）

1. 节约了临时对象的构造和析构成本；
2. 避免了派生对象的切割问题，派生对象在值传递过程中丢失了派生所特有的性质。

```c++
class Window{
public:
    ...
    std::string name() const;
    virtual void display() const;
};

class WindowWithScrollBars : public Window() {
public:
    void display() override const;
};

// pass by value
void printNameAndDisplay(Window w) {
    std::cout << w.name() << std::endl;
    w.display(); // invoke Window::display, no matter what w is
}

// pass by reference
void printNameAndDisplay(const Window& w) {
    std::cout << w.name() << std::endl;
    w.display(); // invoke Window::display or WindowWithScrollBar::display, according to w
}
```

# Item21. 必须返回对象时，别妄想返回其reference

绝不要返回一个指针或者引用指向一个local stack对象，或者返回一个reference指向一个heap-allocated 对象。

> 《Effective C++》 是本好书.