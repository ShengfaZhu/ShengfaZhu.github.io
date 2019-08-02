---
layout: post
title: string与int转换
categories: [C++]
description: some word here
keywords: keyword1, keyword2
---

C++中没有直接的函数可以进行字符串和整型的相互转换。可以手动编写程序进行转化，但要考虑负数，前面有空格，和整数不能以0开头等情况，较为复杂。利用stringstream进行字符串和整型的相互转化，stringstream的头文件为sstream。

下列函数可以方便地实现C++中的字符串与整型的相互转化。
```cpp
string int2str(int n) {
    stringstream ss;
    ss << n;
    return ss.str();
}

int str2int(string s) {
    stringstream ss;
    int n;
    ss << s;
    ss >> n;
    return n;
}
```