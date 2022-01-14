---
title: "单调栈"
date: 2022-01-12T10:01:28+08:00
tags: ["Monotonic"]
categories: ["ACM"]
summary: "基本单调栈知识和典型例题"
---

## 单调栈的定义

单调栈内的元素序列具有单调性, 当有新元素需要添加到栈顶时, 则需要通过弹出部分栈顶元素维持单调性.

我们需要得到的是弹出元素后和添加元素前的栈顶元素; 如果栈顶存放最大元素, 我习惯称作单调递增栈.

![1-1](https://axlis.oss-cn-hangzhou.aliyuncs.com/blog/acm/monotonic/1-1.svg)

## [问题介绍(一)](https://www.luogu.com.cn/problem/P5788)

给出一个长度为n的数组, 求每一个元素后面第一个比他大的元素的下标, 没找到返回-1.

## 问题解答(一)

```c++
int main() {
    int    len;
    cin >> len;

    vector<int> nums(len);
    vector<int> res(len);
    for (int i = 0; i < len; i++) cin >> nums[i];

    stack<int> dec; // 单调递减栈.
    for (int i = len - 1; i >= 0; i--) {
        // 维持栈的单调性, 丢弃.
        while (!dec.empty() && nums[dec.top()] <= nums[i]) dec.pop();
        // 栈顶元素就是第一个比它大的元素.
        if (dec.empty()) res[i] = -1;
        else             res[i] = dec.top();
        // 维持栈的单调性, 添加.
        dec.push(i);
    }

    for (int i : res) {
        cout << i << ' ';
    }
}
```

解决问题时, 我们配合单调递减栈倒着找结果; 由于添加的元素比丢弃元素大, 后续遍历结果不可能是丢弃的元素.

## [问题介绍(二)](https://www.spoj.com/problems/HISTOGRA/)

给出一个单元格的宽固定(1)的直方图, 请找出面积最大的子矩形.

## 问题解答(二)

```c++
int main() {
    int    len;
    while (cin >> len && len > 0) {
        vector<int> nums(len);
        vector<int> res(len, -1);
        for (int i = 0; i < len; i++) cin >> nums[i];

        stack<int> reverse; // 单调递增栈.
        for (int i = len - 1; i >= 0; i--) {
            // 维持栈的单调性, 丢弃.
            while (!reverse.empty() && nums[reverse.top()] >= nums[i]) reverse.pop();
            // 栈顶元素就是第一个比它小的元素.
            if (reverse.empty()) res[i] += len - i;
            else             res[i] += reverse.top() - i;
            // 维持栈的单调性, 添加.
            reverse.push(i);
        }

        stack<int> forward; // 单调递增栈.
        for (int i = 0; i < len; i++) {
            // 维持栈的单调性, 丢弃.
            while (!forward.empty() && nums[forward.top()] >= nums[i]) forward.pop();
            // 栈顶元素就是第一个比它小的元素.
            if (forward.empty()) res[i] += i - (-1);
            else             res[i] += i - forward.top();
            // 维持栈的单调性, 添加.
            forward.push(i);
        }

        long long area = (long long)res[0] * (long long)nums[0];
        for (int i = 1; i < len; i++) {
            area = max(area, (long long)res[i] * (long long)nums[i]);
        }

        cout << area << endl;
    }
}
```

单调栈能让我们以$O(n)$的时间复杂度求出每个元素两侧第一个比它小的元素.

| Right First Element | Left First Element |
| ------------------- | ------------------ |
| Reverse Scan        | Forward Scan       |

注意事项:

* 丢弃元素的条件(相等的条件).
* 栈空的特殊处理(没有找到).