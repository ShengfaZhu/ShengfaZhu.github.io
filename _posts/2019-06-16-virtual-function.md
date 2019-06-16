---
layout: post
title: 虚函数
categories: [C++]
description: some word here
keywords: keyword1, keyword2
---

# 1 虚函数

C++中的虚函数是实现**运行多态**的重要工具。关键字为virtual。

下面程序中定义了基类Base和派生类Deriv,派生类中有与基类中的同名函数print().如果**定义一个基类指针，但是将其指向派生类对象**。那么将指针的类型，调用基类中的print()函数。

```cpp
#include <iostream>
using namespace std;

class Base {
public:
    void print() {
        cout << "Base!!" << endl;
    }
};

class Deriv : public Base {
public:
    void print() {
        cout << "Deriv!!" << endl;
    }

};

int main() {
    Base *p;//基类指针
    p = new Deriv();// 派生类对象
    p->print();
    system("pause");
    return 0;
}
```
输出为Base!!

下面程序中，定义了基类Base和派生类Deriv1和Deriv2，不过在基类中用virtual关键字对print函数进行了修饰，使之成为了虚函数，只要在基类中添加了virtual，子类中的同名函数自然也就是虚函数了。
```cpp
#include <iostream>
using namespace std;

class Base {
public:
    virtual void print() {
        cout << "Base!!" << endl;
    }
};

class Deriv1 : public Base {
public:
    // 是否加virtual都一样
    void print() {
        cout << "Deriv1!!" << endl;
    }

};

class Deriv2 : public Base {
public:
    void print() {
        cout << "Deriv2!!" << endl;
    }

};

int main() {
    Base *p;
    p = new Deriv1();
    p->print();
    p = new Deriv2();
    p->print();
    system("pause");
    return 0;
}
```
输出：
```cpp
Deriv1!!

Deriv2!!
```

同样是果**定义一个基类指针，但是将其指向派生类对象**，由于是虚函数，将在程序运行时，根据指向的对象，分别调用子类中的print()函数。

# 2 纯虚函数
在基类中用virtual关键字修饰函数，并且申明其= 0。这就定义了纯虚函数。包含纯虚函数的类称为抽象类，抽象类没有具体的对象。因此，下面的程序实际上在编译的时候是报错的，error: invalid new-expression of abstract class type 'Base'

```cpp
class Base {
public:
    // pure virtual function
    virtual void print() = 0;
};

int main() {
    Base *p = new Base();
    system("pause");
    return 0;
}

```
在子类中应当对纯虚函数进行具体的实现。如果在子类中，没有对纯虚函数的具体实现，相当于子类直接将纯虚函数继承过来，那么子类还是一个抽象类，依旧不能new出对象。



```cpp
class Base {
public:
    virtual void print() = 0;
};

class Deriv : public Base {
public:
    void print() {
        cout << "Deriv1!!" << endl;
    }

};

int main() {
    Base *p;
    p = new Deriv();
    p->print();
    system("pause");
    return 0;
}
```
当子类成员函数的申明和实现分开时，可以在纯虚函数申明之后加override关键字，强制保证纯虚函数的实现。

```cpp
class Base {
public:
    virtual void print() = 0;
};

class Deriv : public Base {
public:
    // override关键字保证必须有该函数的实现
    void print() override;

};

void Deriv::print() {
    cout << "Deriv!!" << endl;
}

int main() {
    Base *p;
    p = new Deriv();
    p->print();
    system("pause");
    return 0;
}
```


