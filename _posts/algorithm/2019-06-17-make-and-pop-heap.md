---
layout: post
title: make_heap(), pop_heap()函数
categories: [C++]
description: C++建堆
keywords: make heap, pop heap
---

c++中的make_heap(), pop_heap()的头文件为algorithm。作用与priority_queue中的成员函数相同，可以单独使用。

# 使用方法

**make_heap**
在容器范围内，就地建堆，保证最大值在所给范围的最前面，其他值的位置不确定

**pop_heap**
将堆顶(所给范围的最前面)元素移动到所给范围的最后，并且将新的最大值置于所给范围的最前面

**push_heap**
当已建堆的容器范围内有新的元素插入末尾后，应当调用push_heap将该元素插入堆中。

详见下面代码：

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<int> nums = {4, 5, 1, 3, 2};
    // generate heap in the range of numsector
    make_heap(nums.begin(), nums.end());
    cout << "initial max value : " << nums.front() << endl;
    // pop max value
    pop_heap(nums.begin(), nums.end());
    nums.pop_back();
    cout << "after pop, the max vsalue : " << nums.front() << endl;
    // push a new value
    nums.push_back(6);
    push_heap(nums.begin(), nums.end());
    cout << "after push, the max value : " << nums.front() << endl;
    system("pause");
    return 0;
}
```

```
initial max value : 5
after pop, the max vsalue : 4
after push, the max value : 6
```

# 比较函数

既然需要建堆，就意味着元素之间可以相互比较大小，或者比较元素之间的优先级。如果没有指定比较函数，则默认调用"<",堆顶的元素就是与所有元素进行<比较，全为false。那么，有时需要建堆的元素并没有重载运算符<，或者使用希望按其他的方式建堆,此时就需要自定义比较函数。

下面的代码为采用匿名函数定义的比较函数，可以把最小的元素置于堆顶。

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    // define cmp function
    auto cmp = [](const int x, const int y) { return x > y;};
    vector<int> nums2 = {40, 50, 10, 30, 20};
    // generate heap in the range of numsector
    make_heap(nums2.begin(), nums2.end(), cmp);
    cout << "initial max value : " << nums2.front() << endl;
    // pop max value
    pop_heap(nums2.begin(), nums2.end(), cmp);
    nums.pop_back();
    cout << "after pop, the max vsalue : " << nums2.front() << endl;
    // push a new value
    nums2.push_back(0);
    push_heap(nums2.begin(), nums2.end(), cmp);
    cout << "after push, the max value : " << nums2.front() << endl;
    system("pause");
    return 0;
}
```
```
initial max value : 10
after pop, the max vsalue : 20
after push, the max value : 0
```

> 注意匿名函数的类型只能是auto.