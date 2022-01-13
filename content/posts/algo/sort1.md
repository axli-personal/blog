---
title: "快速排序"
date: 2022-01-13T13:15:56+08:00
tags: ["Sort"]
categories: ["Algorithm"]
summary: "各种快速排序的详细讨论和分析"
---

## 相关材料

[Algorithm 4th Edition By Robert Sedgewick](https://algs4.cs.princeton.edu/23quicksort/)

## 基本快速排序

快速排序是Hoare在1960年发明的算法, 通过双指针不断切分数组.

### 实现代码

```c++
template<typename T>
void ClassicSort::Quick(T* data, int lo, int hi) {
    if (lo >= hi) return;

    int i = lo, j = hi + 1;
    T ref = data[lo];

    while (true) {
        while (data[++i] < ref) if (i == hi) break;
        while (data[--j] > ref) if (j == lo) break;

        if (i >= j) break;
        exchange(data, i, j);
    }

    exchange(data, lo, j);

    Quick(data, lo, j - 1);
    Quick(data, j + 1, hi);
}
```

### 注意事项:

* 原地切分: 基于拷贝的切分十分糟糕.
* 随机性: 提前随机打乱或随机选择切分参考对象, 随机性能让我们可以通过概率去预测运行时间的分布.
* 健壮性: 实现任何快速都要十分小心, 时刻注意越界, 循环, 无限递归等问题.

### 性能分析

快速排序的平均时间复杂度为$O(n \log n)$, 最坏情况下时间复杂度为$O(n^2)$, 但良好的随机性能在很大程度上避免最坏情况的发生.

## 算法改进

### 取样切分

我们可以将从数组中抽取少量样本(3~5), 并使用它们的中位数作为切分参考对象, 来提高切分效果.

### 重复元素

在含有大量重复元素时, 基本快速排序的平均时间复杂度$O(n \log n)$并不是最优的.

## 三向切分快速排序

为了处理含有大量重复元素的数据, 在Hoare提出算法不久后就出现了下面的这段代码.

```c++
template<typename T>
void ClassicSort::CenterQuick3Way(T *data, int lo, int hi) {
    if (lo >= hi) return;

    int lt = lo, gt = hi, i = lo + 1;
    T ref = data[lo];

    while (i <= gt) {
        if      (data[i] < ref) exchange(data, i++, lt++);
        else if (data[i] > ref) exchange(data, i, gt--);
        else                    i++;
    }

    CenterQuick3Way(data, lo, lt - 1);
    CenterQuick3Way(data, gt + 1, hi);
}
```

但是我们很快发现在重复元素不多的情况下, 它比基本快速排序使用了更多次交换; 并且它最终因此没有流行开来.

## 两侧三向切分快速排序

又过了不久后, Bently和Mcllroy找到了一个更聪明的办法来解决这个问题(把重复的元素先暂时放在两侧).

```c++
template<typename T>
void ClassicSort::SideQuick3Way(T *data, int lo, int hi) {
    if (lo >= hi) return;

    int i = lo, j = hi + 1;
    int p = lo, q = hi + 1;

    T ref = data[lo];
    while (true) {
        while (data[++i] < ref) if (i == hi) break;
        while (data[--j] > ref) if (j == lo) break;

        // i == j: 指针有可能会一起停止和切分参考对象相等的位置.
        // data[i] == ref: 指针可能一直向右走并因为越界而停下来.
        if (i == j && data[i] == ref) exchange(data, ++p, i);
        if (i >= j) break;

        
        exchange(data, i, j);

        // 将相等的元素放置在两侧.
        if (data[i] == ref) exchange(data, ++p, i);
        if (data[j] == ref) exchange(data, --q, j);
    }

    i = j + 1;
    for (int k = lo; k <= p; k++) {
        exchange(data, k, j--);
    }
    for (int k = hi; k >= q; k--) {
        exchange(data, k, i++);
    }

    SideQuick3Way(data, lo, j);
    SideQuick3Way(data, i, hi);
}
```

三向切分快速排序的额外交换用于和切分参考对象不等的元素身上, 而两侧三向切分快速排序则恰恰相反, 不过最后我们需要将两侧元素移动到中间.

## 实现声明

```c++
class ClassicSort {
public:
    ClassicSort()  = delete;
    ~ClassicSort() = delete;

    template<typename T>
    static void Quick(T* data, int lo, int hi);

    template<typename T>
    static void CenterQuick3Way(T* data, int lo, int hi);

    template<typename T>
    static void SideQuick3Way(T* data, int lo, int hi);

private:
    template<typename T>
    inline static void exchange(T* data, int i, int j) {
        T temp  = data[i];
        data[i] = data[j];
        data[j] = temp;
    }
};
```