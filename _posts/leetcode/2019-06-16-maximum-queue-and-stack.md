---
layout: post
title: 最大值队列、最大值栈
categories: [LeetCode]
description: 实现最大值队列和栈
keywords: 最大值, 双向队列
---

要求实现一个队列或栈，并且在$$O(1)$$的时间内，完成pop()、push()和max()的操作。

# 1 最大值队列
> 《剑指Offer》面试题59

用一个正常的队列实现数据的插入和删除，满足FIFO的关系。用一个**双向队列**保存当前队列中的最大值，并且保证当前最大值始终位于双向队列的前端。如何维护这个双向队列呢？

新数据插入时，将双向队列后部所有比新数据小的元素删除，因为这些元素不可能是未来的最大值了。再将新数据插入到双向队列的后部。

数据删除时，判断双向队列前部的数据的下标是否等于或小于删除数据的下标(实际上只能等于)，如果等于则将双向队列的前部数据删除。这就是为何保持最大值应当使用双向队列，以及为何一定要使得数据中有下标的属性。

```cpp
#include <iostream>
#include <queue>
#include <deque>
#include <stdexcept>// logic_error头文件
#include <exception>// exception头文件
using namespace std;

template<typename T>
class QueueWithMax{
private:
    // index在判断最大值是否被pop时需要
    struct InternalData {
        T num;
        int index;
    };
    // 存储原始数据的队列
    queue<InternalData> data;
    // 存储最大值以及可能最大值的双向队列
    deque<InternalData> maximums;
    // push时生成index
    int curr_index = 0;

public:
    void push_back(T num) {
        InternalData curr = {num, curr_index};
        curr_index++;
        data.push(curr);
        while (!maximums.empty() && maximums.back().num < curr.num)
            maximums.pop_back();
        maximums.push_back(curr);
    }

    void pop_back() {
        if (data.empty()) {
            logic_error ex("Queue is empty!");
            throw new exception(ex);
        }
        InternalData curr = data.front();
        data.pop();
        if (!maximums.empty() && maximums.front().index <= curr.index) 
            maximums.pop_front();
    }
    // const表示传入的this指针为常指针，意味着该
    // 函数不能对任何成员变量进行修改
    T max() const {
        if (maximums.empty()) {
            logic_error ex("Queue is empty!");
            throw new exception(ex);
        }
        // 最大值始终位于双向队列的前端
        return maximums.front().num;
    }
};

int main() {
    QueueWithMax<int> q;
    q.push_back(2);
    q.push_back(6);
    q.push_back(3);
    q.push_back(4);
    cout << q.max() << endl;
    q.pop_back();
    q.pop_back();
    cout << q.max() << endl;
    system("pause");
    return 0;
}
```

# 2 最大值栈
> 《剑指Offer》面试题30

最大值栈用**两个栈**实现相对简单，可以用一个stack保存原始数据，一个stack保存最大值。

新数据插入时，若新数据大于辅助栈顶数据，则将其压入辅助栈；若小于栈顶数据，则将辅助栈栈顶数据再次压缩栈中。

```cpp
#include <iostream>
#include <stack>
#include <stdexcept>
#include <exception>
using namespace std;

template<typename T>
class StackWithMax{
private:
    stack<T> data;
    stack<T> maximums;

public:
    void push_back(T num) {
        data.push(num);
        if (maximums.empty() || num > maximums.top()) maximums.push(num);
        else maximums.push(maximums.top());
    }

    void pop_back() {
        if (data.empty() || maximums.empty()) {
            logic_error ex("Stack is empty!");
            throw new exception(ex);
        }
        data.pop();
        maximums.pop();
    }

    T max() const {
        if (data.empty() || maximums.empty()) {
            logic_error ex("Stack is empty!");
            throw new exception(ex);
        }
        return maximums.top();
    }
};

int main() {
    StackWithMax<int> st;
    st.push_back(4);
    st.push_back(5);
    cout << st.max() << endl;
    st.pop_back();
    cout << st.max() << endl;
    st.push_back(2);
    cout << st.max() << endl;
    st.push_back(10);
    cout << st.max() << endl;
    st.pop_back();
    cout << st.max() << endl;
    system("pause");
    return 0;
}
```
其实也可以**不借助辅助栈**，而是借助O(1)空间实现最大栈。

> LeetCode 155. Min Stack

定义一个变量max_num，始终记录当前栈中的最大值。显然max_num应当随着数据的插入和删除及时地更新。那么原data stack就不能存放原数据了，需要存放与最大值关联的数据.

当插入新数据x时，data中压入x-max_num。如果x>max_num,压入的数据为正数，并更新max_num；反正，压入的数据为负数，不更新max_num。

当删除数据时，栈顶元素为x。如果x<0,说明栈顶元素不是最大值，将其删除即可；反正，栈顶元素为当前的最大值，需要将max_num进行更新为前一个最大值，max_num = max_num - x.

```cpp
#include <iostream>
#include <stack>
#include <stdexcept>
#include <exception>
using namespace std;

template<typename T>
class StackWithMax{
private:
    stack<T> data;
    T max_num;

public:
    void push_back(T num) {
        if (data.empty()) {
            data.push(0);
            max_num = num;
        }
        else {
            data.push(num - max_num);
            if (num > max_num) max_num = num;
        }
    }

    void pop_back() {
        if (data.empty()) {
            logic_error ex("Stack is empty!");
            throw new exception(ex);
        }
        if (data.top() > 0) {
            max_num = max_num - data.top();
        }
        data.pop();
    }

    T max() const {
        if (data.empty()) {
            logic_error ex("Stack is empty!");
            throw new exception(ex);
        }
        return max_num;
    }
};
```

