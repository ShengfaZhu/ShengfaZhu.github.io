---
layout: post
title: 划分partition
categories: [Algorithm]
description: 划分, 三划分
keywords: quicksort, partition
---

# quicksort中的快速划分

快速排序quicksort的核心是对无序向量进行快速划分，选取一个元素作为轴点(pivot)对向量进行划分，确保比轴点大的元素在轴点之后，比轴点小的元素在轴点之前，将原向量划分为两个子向量。

**算法思想**：
1. 取一元素为轴点(pivot)，不妨取首元素为轴点，并将轴点的值备份；
2. 从向量的起始(low)和末尾(high)同时进行扫描；
3. 若nums[high] < pivot, 将其换到nums[low];若nums[low] > pivot,将其换到nums[high];
4. 当low与high位置重合后，将备份的pivot值填回nums[low].

```
int partition(vector<int>& nums, int low, int high) {
    // 任取一元素与首元素交换，以交换后的首元素作为基准
    int r = low + rand() % (high - low + 1);
    int tmp = nums[low];
    nums[low] = nums[r];
    nums[r] = tmp;
    int pivot = nums[low];
    // 由向量两端向中部扫描
    while (low < high) {
        while (low < high && pivot <= nums[high])
            high--;
        nums[low] = nums[high];
        while (low < high && pivot >= nums[low])
            low++;
        nums[high] = nums[low];
    }
    nums[low] = pivot;
    return low;
}
```
上述partition算法返回轴点nums向量中的位置.

显然，该划分算法的时间复杂度是$$O(n)$$，空间复杂度为$$O(1)$$.

# 三划分(3-way-partition)
有些时候需要将向量快速划分为三块，例如LeetCode Sort Colors，向量nums中的元素等于0或1或2,要求在排序后，所有的0在最前面，然后是所有的1，最后是所有的2.该问题可以用三划分方法来解决。

与上述的划分算法相同，分别从向量的首尾开始扫描，同时从向量的左侧向右侧扫描，若nums[i] == 0, 则与nums[low]交换，并且low++,i++；若nums[i] == 1, i++;若nums[i] == 2, 则与nums[high]交换，并且high--，注意此时i并不增加。

```
// 3-way partition
void sortColors(vector<int>& nums) {
    if (nums.empty()) return;
    int i = 0, low = 0, high = nums.size() - 1;
    while (i <= high) {
        if (nums[i] == 1) i++;
        else if (nums[i] == 0) swap(nums[i++], nums[low++]);
        else swap(nums[i], nums[high--]);// not i++
    }
}
```
显然，该划分算法的时间复杂度是$$O(n)$$，空间复杂度为$$O(1)$$.
