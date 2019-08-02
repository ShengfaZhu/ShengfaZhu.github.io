---
layout: post
title: const关键字与指针
categories: [C++]
description: some word here
keywords: keyword1, keyword2
---

**const**是C/C++中定义常量的关键字，有利提高代码的鲁棒性和可读性。const经常与指针一起使用，有指针指向的内容为常量和指针本身为常量两种，容易引起混淆。
# 指针指向的内容不变
```cpp
#include <iostream>
using namespace std;

int main() {
    int a[] = {1, 2, 3, 4};
    const int* p1 = a;// 指针内容不能改变,但指针可以移动
    // int const * p1 = a;//与上行定义的指针相同
    p1++;
    cout << *p1 << endl;
    system("pause");
    return 0;
}
```
编译通过，输出结果$$~2~$$，可以移动指针

const int* p1和int const * p1定义的指针是相同的，const和int的相对位置不影响结果，但是const都在*之前。


```cpp
#include <iostream>
using namespace std;

int main() {
    int a[] = {1, 2, 3, 4};
    const int* p1 = a;// 指针内容不能改变,但指针可以移动
    *p1 = 11;
    cout << *p1 << endl;
    system("pause");
    return 0;
}
```
编译失败，不能对该指针内容进行修改

# 指针本身不变


```cpp
#include <iostream>
using namespace std;

int main() {
    int a[] = {1, 2, 3, 4};
    int* const p2 = a;// 指针不可以移动，但指针内容可以改变
    *p2 = 11;
    cout << *p2 << endl;
    system("pause");
    return 0;
}
```
编译通过，输出结果$~11~$，可以修改指针所指向的内容

```cpp
#include <iostream>
using namespace std;

int main() {
    int a[] = {1, 2, 3, 4};
    int* const p2 = a;// 指针不可以移动，但指针内容可以改变
    p2++;
    cout << *p2 << endl;
    system("pause");
    return 0;
}
```
编译失败，不能移动指针

当const关键字在*之后时，const修饰的是指针本身，即指针的指向不能改变，指针不能移动，但是可以修改指针指向的内容。

