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

        stack<int> dec; // 单调递减栈.
        for (int i = len - 1; i >= 0; i--) {
            // 维持栈的单调性, 丢弃.
            while (!dec.empty() && nums[dec.top()] >= nums[i]) dec.pop();
            // 栈顶元素就是第一个比它小的元素.
            if (dec.empty()) res[i] += len - i;
            else             res[i] += dec.top() - i;
            // 维持栈的单调性, 添加.
            dec.push(i);
        }

        stack<int> inc; // 单调递减栈.
        for (int i = 0; i < len; i++) {
            // 维持栈的单调性, 丢弃.
            while (!inc.empty() && nums[inc.top()] >= nums[i]) inc.pop();
            // 栈顶元素就是第一个比它小的元素.
            if (inc.empty()) res[i] += i - (-1);
            else             res[i] += i - inc.top();
            // 维持栈的单调性, 添加.
            inc.push(i);
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