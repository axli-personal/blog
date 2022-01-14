---
title: "单调双端队列"
date: 2022-01-14T15:08:46+08:00
tags: ["Monotonic"]
categories: ["ACM"]
summary: "基本单调双端队列知识和典型例题"
---

## 单调双端队列的定义

单调双端队列内的元素序列具有单调性, 当有新元素需要添加到队尾时, 则需要通过弹出部分队尾元素维持单调性.

我们需要得到的是弹出元素并添加元素后的队头元素; 如果对头存放最大元素, 我习惯称作单调递增双端队列.

另外我们在向队尾添加元素后, 通过弹出队头元素来实现一些约束条件(控制队列的长度).

## [问题介绍(一)](https://www.luogu.com.cn/problem/P1886)

在数组上有一固定长度的滑动窗口, 计算每个位置窗口内元素的最小值和最大值.

## 问题解答(一)

```c++
int main() {
    int    len,   window;
    cin >> len >> window;

    vector<long long> nums(len);
    for (int i = 0; i < len; i++) {
        cin >> nums[i];
    }

    deque<int> dec;
    for (int i = 0; i < window - 1; i++) {
        while (!dec.empty() && nums[dec.back()] >= nums[i]) dec.pop_back();
        dec.push_back(i);
    }

    for (int i = window - 1; i < len; i++) {
        while (!dec.empty() && nums[dec.back()] >= nums[i]) dec.pop_back();
        dec.push_back(i);

        while (dec.back() - dec.front() + 1 > window) dec.pop_front();
        cout << nums[dec.front()] << ' ';
    }
    cout << endl;

    deque<int> inc;
    for (int i = 0; i < window - 1; i++) {
        while (!inc.empty() && nums[inc.back()] <= nums[i]) inc.pop_back();
        inc.push_back(i);
    }

    for (int i = window - 1; i < len; i++) {
        while (!inc.empty() && nums[inc.back()] <= nums[i]) inc.pop_back();
        inc.push_back(i);

        while (inc.back() - inc.front() + 1 > window) inc.pop_front();
        cout << nums[inc.front()] << ' ';
    }
    cout << endl;
}
```