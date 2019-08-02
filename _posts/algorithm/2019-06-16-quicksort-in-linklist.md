---
layout: post
title: 链表快排
categories: [Algorithm]
description: 链表快排
keywords: partition, quicksort, linklist
---

# 链表快排

对于数组的快排算法，核心是进行划分(partition)，确定一个轴点，将小于等轴点的数调整在轴点之前，将大于等于轴点的数调到轴点之后，然后返回轴点的下标。在划分算法中，需要分别从数组的前面和后面对数组进行遍历，后面的下标向前移动。但是在单链表中，并没有向前的指针，无法向链表前面移动。
确定第一个元素的轴点
定义两个指针，fast和slow。（目标是在从头到slow之间的元素均小于等于轴点；slow到fast之间的元素均大于等于轴点）；
fast向后移动，如果fast < pivot，则slow先后移，然后交换slow和fast的值。这样可以保证slow永远是最后一个小于等于pivot的元素，而slow的下一个是大于等于pivot的元素。
如下程序所示：

```cpp
#include <iostream>
using namespace std;

struct ListNode
{
    int value;
    ListNode* next;
    ListNode(int v) : value(v), next(nullptr) {}
};

ListNode* patition(ListNode* begin, ListNode* end) {
    if (begin == end) {
        return begin;
    }
    int pivot = begin->value;
    ListNode* slow = begin, *fast = begin->next;
    while (fast != end) {
        if (fast->value < pivot) {
            slow = slow->next;
            swap(slow->value, fast->value);
        }
        fast = fast->next;
    }
    swap(begin->value, slow->value);
    return slow;
}

void quickSort(ListNode* head, ListNode* end) {
    if (head == end || head == nullptr) {
        return;
    }
    ListNode* pivot = patition(head, end);
    quickSort(head, pivot);
    quickSort(pivot->next, end);
}

int main() {
    vector<int> nums = {4, 2, 5, 3, 7, 9, 0, 1};
    ListNode* head = new ListNode(nums[0]), *p = head;
    for (size_t i = 1; i < nums.size(); ++i) {
        ListNode* curr = new ListNode(nums[i]);
        p->next = curr;
        p = p->next;
    }
    quickSort(head, nullptr);
    p = head;
    while (p) {
        cout << p->value << "\t";
        p = p->next;
    }
    system("pause");
    return 0;
}
```

注意：排序中进行的节点值的交换，因为链表节点的交换太过复杂。