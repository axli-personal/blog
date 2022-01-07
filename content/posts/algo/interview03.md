---
title: "3-数组中重复的数字"
date: 2022-01-07T22:00:00+08:00
tags: ["interview"]
categories: ["algorithm"]
summary: "剑指Offer面试题3的归纳"
---

## 问题介绍(一)

在一个长度为n的数组中, 每个元素都是[0, n)内的数字. 尝试找出数组中任意重复的元素.

## 问题解答(一)

一看到到找出重复元素这个问题, 就会不自然的想到`HashSet`. 这样我们算法在运行效率上就会依赖于`HashSet`的高效实现.

在理想情况下, 基于`HashSet`的实现时间复杂度应该在$O(n)$, 并且也是一般化的解法, 但`HashSet`额外开辟了一定空间.

我们可以根据元素给定范围的特殊性(正好是数组下标范围), 实现一个空间占用更小的算法, 但需要修改原数组.

```c++
bool find_duplicates(int* array, int size, int* duplicates) {
    if (array == nullptr || duplicates == nullptr) return false;

    // 范围检查.
    for (int i = 0; i < size; i++) {
        if (array[i] < 0 || array[i] >= size) {
            return false;
        }
    }
    
    for (int i = 0; i < size; i++) {
        if (array[i] == i) continue;
        // 找到重复的数字.
        if (array[array[i]] == array[i]) {
            *duplicates = array[i];
            return true;
        }
        // 放到对应位置.
        int temp = array[array[i]];
        array[array[i]] = array[i];
        array[i] = temp;
    }
    return false;
}

void test() {
    int   nums[]{1, 4, 3, 5, 0, 3};
    auto  duplicates = new int;
    
    cout << find_duplicates(nums, 6, duplicates) << ends << *duplicates << endl;
    cout << find_duplicates(nullptr, 0, nullptr) << ends << *duplicates << endl;
    
    delete duplicates;
}
```
