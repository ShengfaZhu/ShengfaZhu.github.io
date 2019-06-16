---
layout: post
title: 位操作
categories: [Alorithm, LeetCode]
description: 位操作介绍
keywords: 位操作, 求1的数量
---

# 1. 位操作
位操作是把数字用二进制表示后，在每一位上对0和1进行直接操作。C++位操作有6种，分别是与、或、异或、取反、左移和右移

符号 | 操作 | 运算规则
---|--- | ---
& | 位与 | 都为1时为1，其他为0
~ | 位或 | 都为0时为0，其他为1
^ | 异或 | 相同为0，不同为1
~ | 取反 | 0变为1,1变为0
\<\< | 左移 | 全部左移，最低位补零
\>\> | 右移 | 全部右移，最高位补零

* * *

> 注意：
> - 位操作仅对整型有效，对浮点型进行位操作在编译时会报错。
> - 在c++中，位操作符（&，|和^）的优先级比相等运算符(==和!=)要低！！
因此，在进行位操作的判断时候，一定要在判断相等前面加上括号，否则会先进行相等运算符，然后再进行位操作运算。


# 2. 位操作的基本用法
位操作可以获得更快的运行速度，位操作可以用来求1/2，判断奇偶、交换两个整型数、变换正负号

## 2.1. 求1/2
右移1位与整型的除2结果相同，可以用在二分查找中。
```cpp
int a = 5
cout << (a >> 1) << endl;
```

## 2.2. 判断奇偶
在整数的二进制表示中，偶数的最后一位为0，奇数的最后一位为1，可以据此判断奇偶性。
```cpp
int a = 5;
if (a & 1 == 0)
    cout << "even" << endl;
else
    cout << "odd" << endl;
```

## 2.3. 交换两个整数
一般来说，交换两个整数需要借助第三个数，利用异或操作可以方便地交换两个整数。

**异或有四个重要的性质**：

- 异或满足结合律
- 异或满足交换律
- 相同数的异或为0，A ^ A = 0
- 和0的异或为本身，A ^ 0 = A

```cpp
int a = 5, b = 6;
a = a ^ b;
b = a ^ b;
a = a ^ b;
cout << a << b << endl;
```

## 2.4. 变换正负号
将正数变为负数，负数变为正数，并且绝对值不变。
根据正负数二进制编码规则，变换正负号仅需取反再加一即可。
```cpp
int a = 5, b = -6;
a = ~a + 1;
b = ~b + 1;
cout << a << endl << b << endl;
```

# 3. 位操作的进阶应用
利用位操作在处理一些算法题时，可以获得意向不到的效果，同时提高时间复杂度和空间复杂度等。

## 3.1. 二进制中1的个数
统计一个整数的二进制表示中1出现的次数并不难，对于一个4字节的整型，只需要遍历其32位，加以统计即可。
```cpp
int countOnes(const int& num) {
    int count = 0;
    int mask = 1;
    for (int i = 0; i < 32; ++i) {
        if ((num & mask) != 0) count++;
        mask = mask << 1;
    }
    return count;
}
```
上面的做法并不复杂也不慢，对于任何数字都需要循环32次。但是当数字中有很多位0时，如果可以跳过，那就可以加速算法。
```cpp
int countOnes(int num) {
    int count = 0;
    while (num) {
        num = num & (num - 1);
        count++;
    }
    return count;
}
```
二进制表示中，****10000 - 1 = ****01111。通过上面程序中的num-1再求与操作，可以跳过32位中的所有非零位，程序更快。

## 3.2 判断是否为2的幂

如果一个整数是2的幂，则该数的二进制表示一定为00...0100..00，有且仅有一个1.可以参考3.1节中的技巧，用位操作快速判断一个整数是否为2的幂次。
```cpp
bool isPowerOfTwo(int num) {
    return (num & (num - 1)) == 0;
}
```
有可借鉴的思路判断是否是3、4的乘方。

[LeetCode 342. Power of Four](https://leetcode.com/problems/power-of-four/description/)

[LeeCode 326. Power of Three](https://leetcode.com/problems/power-of-three/description/)

## 3.3. 只出现一次的数字
一个一维数组中只出现一次的数字，其他数字均出现了2次或者多次，如何在$O(n)$的时间复杂度、$O(1)$的空间复杂度内，找到这个只出现一次的数字呢？

### 3.3.1. 一个数字出现一次，其余数字出现两次
如果其余数字均出现了两次，那么就比较容易.可以利用异或操作。在2.3节中提到了异或操作的四个重要性质。
```C++
int findOccurOnce(const vector<int>& nums) {
    int ans = 0;
    for (int n : nums) ans = ans ^ n;
    return ans;
}
```
上述程序相当于对数组中所有的元素做了异或 $ans=a_{1}~xor~a_{2}~xor~a_{3}~xor~...~a_{n}$
由于异或的交换律和结合律，总是可以把两个相同的元素交换到一起。而两个相同数字的异或为0，0与其他所有数的异或都是其本身，因此最后的结果就是那个出现了一次的元素。这种做法非常tricky。

### 3.3.2. 两个数字出现一次，其余数字出现两次
如果有两个数字只出现了一次，其余数字均出现了两次，那么如何找到这两个数字呢？

用3.2.1中的将所有元素全部异或一遍的做法，最后得到的结果是那两个只出现一次元素的异或值ans。两个出现一次的数相同的位上为0，不同的位上为1，可以根据ans中为1的位，将所有元素分成两拨，这样两拨中就仅含一个出现一次的元素，又可以愉快地使用3.2.1中的方法了。
实现如下：
> 《剑指Offer》2nd ed：275-277

```cpp
unsigned int findFirstOne(int num) {
    unsigned int index = 0;
    while (((num & 1) == 0) && (index < 8 * sizeof(int))) {
        num = num >> 1;
        index++;
    }
    return index;
}

void findOccurOnce(const vector<int>& nums, int* num1, int* num2) {
    if (nums.empty()) return;
    int resXOR = 0;
    for (int n : nums) resXOR = resXOR ^ n;
    unsigned int index = findFirstOne(resXOR), mask = 1;
    while (index--) mask = mask << 1;
    *num1 = *num2 = 0;
    for (int n : nums) {
        if ((n & mask) != 0) *num1 = *num1 ^ n;
        else *num2 = *num2 ^ n;
    }
}
```

### 3.3.3. 一个数字出现一次，其余数字出现3次
如果其他数字均出现了三次，就意味着无法使用全部异或一遍的处理方法。如果将所有元素在的对应数位相加，然后再把每位相加之和对3求余，每位所得的余数就是出现一次数字对应位上的数,然后再把该数表示成整型形式。
```cpp
int findOccurOnce(const vector<int>& nums) {
    vector<int> sumBit(8 * sizeof(int), 0);
    for (int n : nums) {
        int mask = 1;
        for (int i = 0; i < sumBit.size(); ++i) {
            if ((n & mask) != 0) sumBit[i]++;
            mask = mask << 1;
        }
    }
    int num = 0;
    // from high bit to low bit
    for (int i = sumBit.size() - 1; i >= 0; --i) {
        num = num << 1;
        num = num + (sumBit[i] % 3);
    }
    return num;
}
```








