---
layout: post
title: vector空间
categories: [C++]
description: STL Vector的空间问题
keywords: vector, size, capacity
---

在c++使用vector或者string容器时，所需容器的空间超过容器当前的空间时，会进行扩容，一般都是扩大为原来的两倍。如果一直push_back，容器将会在内部自行扩容，将会影响程序的性能。

如果在使用容器之前就能知道vector的大小，可以先用reserve函数预先分配空间，这样就可以避免反复地扩容。
```cpp
void reserve (size_type n);
Request a change in capacity
Requests that the vector capacity be at least enough to contain n elements.
If n is greater than the current vector capacity, the function causes the 
container to reallocate its storage increasing its capacity to n (or greater).
In all other cases, the function call does not cause a reallocation and the 
vector capacity is not affected.
This function has no effect on the vector size and cannot alter its elements.
```

**关于vector的大小问题：**
- vector的大小有两个相关属性，size()和capacity()。
- capacity指的是现在分配给这个vector存储空间大小；
- size指的是当前这个容器中元素的数量；
- capacity一定是大于等于size的，当size超过capacity就需要进行扩容操作；
- capacity能不能无线增长呢？答案显然是否定的，vector的max_size()属性规定了潜在最大的空间。