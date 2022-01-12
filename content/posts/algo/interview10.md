---
title: "10-斐波那契数列"
date: 2022-01-12T11:20:40+08:00
tags: ["interview"]
categories: ["algorithm"]
summary: "剑指Offer面试题10的归纳"
---

## 问题介绍(一)

求斐波那契(Fibonacci)数量的第n项.

## 问题解答(一)

```c++
long long fibonacci(int index) {
    if (index < 0) throw runtime_error("invalid parameter with negative index");

    if (index == 0) return 0;
    if (index == 1) return 1;

    long long pre = 0, tail = 1, res = 1;
    for (int i = 1; i < index; i++) {
        res  = pre + tail;
        pre  = tail;
        tail = res;
    }

    return res;
}
```

斐波那契第n项可以用$O(n^2)$的递归, $O(n)$的循环, $O(\log n)$的矩阵公式(不实用)求解, 求解时可以使用`long long`.