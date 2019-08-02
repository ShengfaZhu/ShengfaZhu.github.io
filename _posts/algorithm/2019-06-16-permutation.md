---
layout: post
title: 全排列
categories: [Algorithm]
description: 递归方法实现全排列
keywords: 全排列, 递归 
---

# 全排列

给定组数字或者一个字符串，长度为n,打印出其全排列，例如：

abc的全排列有：abc, acb, bac, bca, cab, cba六种。

可以用递归的方法求解，即先固定第一个元素，然后求出后n-1个元素的全排列。当然第一个元素可能是任字符串中的任一个字符。C++代码如下：
```cpp
#include <iostream>
#include <string>
using namespace std;

void permute(string str, int k) {
	if (k == str.size() - 1) {
		cout << str << endl;
	}
	else if (k >= str.size()) return;
	for (int i = k; i < str.size(); i++) {
		swap(str[i], str[k]);
		permute(str, k + 1);
		swap(str[i], str[k]);
	}
}

void permute(string str) {
	if (str.empty()) return;
	permute(str, 0);
}

int main()
{
	string str = "abcdef";
	permute(str);
	system("pause");
	return 0;
}
```
# 部分元素的全排列

给定组数字或者一个字符串，长度为n，从中任取m个元素，打印出m个元素的排列。

该排列的数量是$$C_{n}^{m}$$个.

依旧可以用的方法求解，即先固定第一个元素，然后求出后n-1个元素的中的m-1元素的全排列。当然第一个元素可能是任字符串中的任一个字符。C++代码如下：
```cpp
#include <iostream>
#include <string>
using namespace std;

void permute(string str, int k, int len_substr) {
	if (len_substr == 0) {
		cout << str.substr(0, k) << endl;
	}
	for (int i = k; i < str.size(); i++) {
		swap(str[i], str[k]);
		permute(str, k + 1, len_substr - 1);
		swap(str[i], str[k]);
	}
}

void permute(string str, int len_substr) {
	if (str.size() < len_substr) return;
	permute(str, 0, len_substr);
}
int main()
{
	string str = "abcdef";
	int len_substr = 3;
	permute(str, len_substr);
	system("pause");
	return 0;
}
```
