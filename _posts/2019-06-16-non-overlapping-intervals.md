---
layout: post
title: 不重叠区间
categories: [LeetCode]
description: 求取一系列区间内的不重叠区间
keywords: keyword1, keyword2
---


求出一系列区间中最多不重叠区间的数量[non-overlapping intervals](https://leetcode.com/problems/non-overlapping-intervals/)。
 
```cpp
int eraseOverlapIntervals(vector<Interval>& intervals) {
    if (intervals.empty()) return 0;
    // sort according to end
    sort(intervals.begin(), intervals.end(), [](Interval& x, Interval& y){return x.end < y.end;});
    
    // count
    int count = 1, int_end = intervals.at(0).end;
    for (size_t i = 0; i < intervals.size(); ++i) {
        if (intervals.at(i).start >= int_end) {
            count++;
            int_end = intervals.at(i).end;
        }
    }
    return intervals.size() - count;
}
```

- 必须以区间的末端为排序的依据，这才会保留那些数量多的短区间，放弃那些长的区间。


使用了贪心策略， 在每个局部做最有搜索，最后可以得到全局最优，和动态规划相似，需要满足最有子结构。