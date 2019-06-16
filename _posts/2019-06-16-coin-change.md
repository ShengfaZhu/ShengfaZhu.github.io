---
layout: post
title: 硬币置换
categories: [LeetCode]
description: 动态规划求解硬币置换问题
keywords: 动态规划, 硬币置换
---

LeetCode 322, 518就是关于硬币置换的问题，都可以用动态规划的方式进行解答，但方法稍有不同。
# 问题1
给定不同硬币的面额和钱的总数，计算为了凑其总的钱所需最少硬币的数量，若无法凑齐则输出-1。

记为了凑出$$x$$元所用硬币的最少数量为$$f(x)$$，则由递推关系：

$$
f(x)=\min_{i} \left \{ f(x-n_{i}) + 1\right \}
$$
式中，$$n_{i}$$为面值。


动态规划的代码如下：
```cpp
int coinChange(vector<int>& coins, int amount) {
    int Max = amount + 1;
    vector<int> dp(amount + 1, Max);
    dp[0] = 0;
    for (int i = 1; i <= amount; i++) {
        for (int j = 0; j < coins.size(); j++) {
            if (coins[j] <= i) {
                dp[i] = min(dp[i], dp[i - coins[j]] + 1);
            }
        }
    }
    return dp[amount] > amount ? -1 : dp[amount];
}
```

# 问题2

给定硬币的面值和总的金钱，假设每种面值的硬币数量都是无限的，求出有多少种组合？

该问题与问题1稍有不同，但是同样可以用动态规划的方法进行求解。


```cpp
int change(int amount, vector<int>& coins) {
    vector<int> dp(amount + 1, 0);
    dp[0] = 1;
    for (int j = 0; j < coins.size(); j++) {
        for (int i = 1; i <= amount; i++) {
            if (coins[j] <= i) {
                dp[i] = dp[i] + dp[i - coins[j]];
            }
        }
    }
    return dp[amount];
}
```
上述代码非常有技巧，外层循环为对面值，内层循环为金钱总数。例如面值为“1，2，5”,那么外层循环第一次就是仅用1能够有多少种组合，第二次就是仅用1、2能够有多少种组合。这样能做到不重复也不遗漏。
