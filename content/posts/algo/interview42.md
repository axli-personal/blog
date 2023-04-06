---
title: "剑指Offer - 连续子数组最大和"
date: 2022-11-24T13:23:45+08:00
tags: []
categories: ["Algorithm"]
summary: "TODO"
---

## 连续子数组最大和

### Greedy

举例寻找规律: 如果当前连续子数组和不是正数, 那么还不如从下一个数开始.

### Dynamic Programming

$$
f(i) = \begin{cases}
nums[i] & f(i-1) \le 0 \\\
f(i-1) + nums[i] & f(i-1) > 0
\end{cases}
$$
