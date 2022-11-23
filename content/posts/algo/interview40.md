---
title: "剑指Offer - 最小的k个数"
date: 2022-11-23T19:56:14+08:00
tags: []
categories: ["Algorithm"]
summary: "剑指Offer面试题40"
---

## 最小的k个数

### 解法一

找出第k大的数, 使用Partition实现. 最后一次Partition将比k大的数都分到了数组的右边.

### 解法二

使用容量为k的优先队列.
