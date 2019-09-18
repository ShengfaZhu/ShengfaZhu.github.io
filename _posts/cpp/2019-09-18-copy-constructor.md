---
layout: post
title: 默认拷贝构造函数的拷贝行为
categories: [C++]
description: some word here
keywords: keyword1, keyword2
---

在用户定义的类中，即使用户没哟定义，c++编译器依旧会默默地编写默认构造函数、析构函数、拷贝构造函数和赋值运算符。

参考effective c++书中的item05，编译器默默写的拷贝构造函数的会将调用non-static成员拷贝构造函数初始化non-static成员。

# 默认拷贝构造的行为

下面代码中的Student类的私有成员变量有int型id_和指针型的name_，其中id_是内存分配在stack区,而name_则是在heap上动态分配内存。

对于下面的函数，如果没有定义拷贝构造函数，从输出结果可以看到a和b的name_的地址是一样的。当在主函数中调用b(a)的时候，将会调用编译器编写的默认拷贝构造函数。而**默认拷贝函数会将所有stack区的变量进行复制，name_相当于地址复制了，指向同一块动态内存**。因此，当在析构函数中delete动态内存的时候，会由于释放了两次同一块动态内存而出现错误。

```cpp
#include <iostream>

class Student {
   public:
    Student();

    Student(int id, char* name) {
        id_ = id;
        name_ = new char[strlen(name) + 1];
        strcpy(name_, name);
    }

    ~Student() {
        delete name_;
        std::cout << "deconstructor called." << std::endl;
    }

    void print() {
        std::cout << "id : " << id_ << std::endl;
        std::cout << "name : " << static_cast<void*>(name_) << std::endl;
    }

   private:
    int id_;
    char* name_;
};


int main() {
    Student a(1, "zhangshan");
    Student b(a);
    a.print();
    b.print();
    return 0;
}
```

```cpp
id : 1
name : 0x7fcefa400630
id : 1
name : 0x7fcefa400630
deconstructor called.
rationalo(9872,0x1024185c0) malloc: *** error for object 0x7fcefa400630: pointer being freed was not allocated
rationalo(9872,0x1024185c0) malloc: *** set a breakpoint in malloc_error_break to debug
[1]    9872 abort      ./rationalo
```


# 自定义拷贝构造函数的条件

从上面的分析中，可以看出当对象的数据成员需要动态分配内存时，则需要自行定义拷贝构造函数。也就是说，当用户需要自动定义析构函数来释放数据成员的内存时，需要自定义拷贝构造函数。

在下面代码中自定义拷贝构造函数，这样a和b对象的name_变量的地址是不一样的，在析构函数中释放动态内存的时候，就不会出现错误。

```cpp
#include <iostream>

class Student {
   public:
    Student();

    Student(int id, char* name) {
        id_ = id;
        name_ = new char[strlen(name) + 1];
        strcpy(name_, name);
    }

    Student(const Student& rhs) {
        this->id_ = rhs.id_;
        this->name_ = new char[strlen(rhs.name_) + 1];
        strcpy(this->name_, this->name_);
    }

    ~Student() {
        delete name_;
        std::cout << "deconstructor called." << std::endl;
    }

    void print() {
        std::cout << "id : " << id_ << std::endl;
        std::cout << "name : " << static_cast<void*>(name_) << std::endl;
    }

   private:
    int id_;
    char* name_;
};


int main() {
    Student a(1, "zhangshan");
    Student b(a);
    a.print();
    b.print();
    return 0;
}
```

输出结果

```cpp
id : 1
name : 0x7fa525400630
id : 1
name : 0x7fa525400690
deconstructor called.
deconstructor called.
```

# 赋值运算符重载


