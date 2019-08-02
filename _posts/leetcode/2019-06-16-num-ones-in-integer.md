---
layout: post
title: 整数中1的数量
categories: [LeetCode]
description: 通过动态规划的算法确定整数的二进制表示中1的数量
keywords: 位运算, 动态规划, 1的数量
---

给定正整数n,求出i(0<=i<=n)的二进制表示中1的数量。

事实上，有如下的关系存在：
$$
f(n) = f(n/2)+n\%2
$$


因为n/2=n>>1(整数除法)

当n为偶数时，n的最后一位为0，因此n与n/2的二进制中1数量相同；

当n为奇数时，n的最后一位为1，因此n的二进制中1数量比n/2多1。

根据上述关系，可以使用动态规划的算法，得到整数二进制表示中1的数量，代码如下：

```cpp
// LeetCode 338. Counting Bits
vector<int> countBits(int num) {
    assert(num >= 0);
    vector<int> count(num+1, 0);
    for (int i = 1; i <= num; i++) {
        count[i] = count[i >> 1] + (i % 2);
    }
    return count;
}
```

