---
layout: post
title: 二分查找
categories: [Alorithm, LeetCode]
description: 有序必二分
keywords: 查找, 选择数组
---

对于在**有序**的向量可以使用二分查找的方法，在\(log(n)\)的时间复杂度内完成查找。应当指出二分查找必须基于“有序”的前提条件。

# 1 基本程序实现
在一个数组中，查找一个数字的基本程序如下所示。
```cpp
int binary_search(const vector<int>& nums, int target) {
    // -1 means target doesn`t exit in nums
    if (nums.empty()) return -1;
    int low = 0, high = nums.size() - 1;
    while (low <= high) {
        int mid = (low + high) >> 1;
        if (nums[mid] == target) return mid;
        else if (nums[mid] < target) low = mid + 1;
        else high = mid - 1;
    }
    return -1;
}
```
实际中，二分查找可以有不同的变形。

# 2 旋转数组的最小数字

输入一个递增排序数组的一个旋转，输出旋转数组的最小元素。例如，数组{3, 4, 5, 1, 2}为{1, 2, 3, 4, 5}的一个旋转，该数组的最小值为1.数组中无重复元素

> LeetCode 153. Find minimum in rotated sorted array 

> 《剑指Offer》(2rd Edition)面试题11

这道题目其实并不是严格意义的有序,但是旋转之后的数组可以有两个有序的子数组构成，并且这两个子数组之间有严格的大小关系(没有重复元素)。

如果令low=0, high = size - 1, mid = (low + high) / 2，那么如果mid在第二个递增数组中，则nums[mid] <= nums[high]，否则mid在必第一个数组中。最小的元素在第二个数组的开头，因此要注意,当mid在第一个数组中时，low = mid + 1,当mid在第二个数组中，high = mid。

```cpp
int findMin(vector<int>& nums) {
    int low = 0, high = nums.size() - 1;
    // no rotation
    if (nums[low] < nums[high]) return nums[low];
    while (low < high) {
        int mid = low + (high - low) / 2;
        if (nums[mid] <= nums[high])
            high = mid;
        else
            low = mid + 1;
    }
    return nums[high];
}
```

上题有个条件是数组无重复，如果去掉数组重复的条件，则会变得更为复杂一些。例如{1, 0, 1, 1, 1}就是有序数组{0, 1, 1, 1, 1}的旋转。因为nums[mid]=nums[low]=nums[high]，此时无法判断mid到底在第一个数组中，还是第二个数组中。此时只能进行顺序查找。

> LeetCode 154. Find minimum in rotated sorted array II

> 《剑指Offer》(2rd Edition)面试题11


```cpp
int find_in_order(vector<int>& nums, int low, int high) {
    int min_value = nums[low];
    for (int i = low + 1; i <= high; ++i) {
        min_value = min(nums[i], min_value);
    }
    return min_value;
}

int findMin(vector<int>& nums) {
    int low = 0, high = nums.size() - 1;
    if (nums[low] < nums[high]) return nums[low];// no rotation
    while (low < high) {
        int mid = low + (high - low) / 2;
        // nums[low] == nums[mid] == nums[high], search in order
        if (nums[mid] == nums[low] && nums[mid] == nums[high])
            return find_in_order(nums, low, high);
        else if (nums[mid] <= nums[high])
            high = mid;
        else
            low = mid + 1;
    }
    return nums[high];
}
```
# 3 在旋转数组中查找目标

在一个旋转的有序数组中查找给定目标值的下标，如果没有该值则返回-1.
> LeetCode 33. Search in Rotated Sorted Array

有了**2**中的分析，可以先找到旋转的点，然后再进行**1**中的二分查找。

记旋转的最小元素的下标为rota, 如果在未旋转数组中下标为i的元素，则该元素在旋转后的数组中的下标为(rota + i) % n.
```cpp
int findMin(vector<int>& nums, target) {
    int low = 0, high = nums.size() - 1;
    while (low < high) {
        int mid = low + (high - low) / 2;
        if (nums[mid] <= nums[high])
            high = mid;
        else
            low = mid + 1;
    }
    int rota = low;
    low = 0, high = nums.size() - 1;
    while (low <= high) {
        int mid = low + (high - low) >> 1;
        % real_mid : index in rotated array
        int read_mid = (rota + mid) % nums.size();
        if (nums[real_mid == target) return real_mid;
        else if (nums[real_mid] < target) low = mid + 1;
        else high = mid - 1;
    }
    return -1;
}
```
可以验证，对于没有旋转的情况，上述算法也是适用的。


