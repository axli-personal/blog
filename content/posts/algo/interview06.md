---
title: "6-从尾到头打印链表"
date: 2022-01-07T22:03:00+08:00
tags: ["interview"]
categories: ["algorithm"]
summary: "剑指Offer面试题6的归纳"
---

## 问题介绍

从尾到头输出链表, 不能修改原来的链表.

## 问题解答

很自然的想到这是一个栈的应用.

```c++
class Node {
public:
    int   val;
    Node* next;
};

/**
  This function can print a linklist reversely.

  When pass in nullptr, print [] instead of empty.
*/
void reverse_print(const Node* head) {
    stack<int> nums;

    while (head != nullptr) {
        nums.push(head->val);
        head = head->next;
    }

    cout << '[';
    while (!nums.empty()) {
        cout << nums.top();
        nums.pop();
        if (!nums.empty()) cout << ',';
    }
    cout << ']' << endl;
}

void test() {
    Node* head = new Node();
    head->val = 1;
    head->next = new Node();
    head->next->val = 2;
    head->next->next = nullptr;

    reverse_print(head);
    reverse_print(nullptr);

    delete head->next;
    delete head;
}
```

海涛分析了递归和栈两种方案, 并且存放的是节点指针, 更具一般性.
