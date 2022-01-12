---
title: "单调栈"
date: 2022-01-12T10:01:28+08:00
tags: ["Math"]
categories: ["ACM"]
summary: "基本单调栈知识和典型例题"
---

## 单调栈的定义

单调栈内的元素序列具有单调性, 当有新元素需要添加时, 则需要通过弹出部分元素维持单调性; 大部分情况下, 弹出的元素被直接丢弃.

![1-1](https://axlis.oss-cn-hangzhou.aliyuncs.com/blog/acm/monotonic/1-1.svg)

## 问题介绍(一)

给出一个长度为n的数组, 求每一个元素后面第一个比他大的元素的下标, 没找到返回-1.

## 问题解答(一)

```c++
int main() {
    int    len;
    cin >> len;

    vector<int> nums(len);
    vector<int> res(len);
    for (int i = 0; i < len; i++) cin >> nums[i];

    stack<int> inc; // 单调递增栈.
    for (int i = len - 1; i >= 0; i--) {
        // 维持栈的单调性, 丢弃.
        while (!inc.empty() && nums[inc.top()] <= nums[i]) inc.pop();
        // 栈顶元素就是第一个比它大的元素.
        if (inc.empty()) res[i] = -1;
        else             res[i] = inc.top();
        // 维持栈的单调性, 添加.
        inc.push(i);
    }

    for (int i : res) {
        cout << i << ' ';
    }
}
```

解决问题时, 我们配合单调递增栈倒着找结果; 由于添加的元素比丢弃元素大, 后续遍历结果不可能是丢弃的元素.