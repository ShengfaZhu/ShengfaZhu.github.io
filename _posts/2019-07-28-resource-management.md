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
- **获得资源后立即放入管理对象内**，上面代码以返回的raw pointer来初始化智能指针，是“资源取得时机便是初始化时机(Resource Acquisition Is Initialization, RAII)”
- **管理对象运用析构函数确保资源被释放**

应当指出，智能指针的析构函数内做的是delete动作，而不是delete[]，因此不应像下面一样在动态分配的数组上使用智能指针。

```cpp
std::shared_ptr<int> spi(new int[1024]);// 会用上错误形式的delete
```

std提供的vector已经可以取代动态内存数组的需求，所有尽量不要使用动态内存数组，而是std::vector<>。

# Item14. 在资源管理类中小心coping行为








> 《Effective C++》 是本好书.