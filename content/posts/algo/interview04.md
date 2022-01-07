---
title: "4-二维数组查找"
date: 2022-01-07T22:01:00+08:00
tags: ["interview"]
categories: ["algorithm"]
summary: "剑指Offer面试题4的归纳"
---

## 问题介绍

我们需要从从二维数组中查找元素, 而二维数组每行和每列都已经递增排序了.

## 问题解答

从右上角开始, 根据大小关系调整查找范围.

```cpp
bool matrix_find(const int* matrix, int rows, int columns, int goal) {
    if (matrix == nullptr) return false;

    // 从右上角开始缩小范围.
    int x = columns - 1, y = 0;
    while (x >= 0 && y < rows) {
        int top_right_corner = matrix[y * columns + x];
        if      (goal < top_right_corner) x--;
        else if (goal > top_right_corner) y++;
        else    return true;
    }

    return false;
}

void test() {
    int matrix[4][4]{
        {1, 2, 8, 9},
        {2, 4, 9, 12},
        {4, 7, 10, 13},
        {6, 8, 11, 15}
    };
    bool result;
    
    result = matrix_find(matrix[0], 4, 4, 7);
    cout << "1: " << (result ? "pass" : "fail") << endl;

    result = matrix_find(matrix[0], 4, 4, 1);
    cout << "2: " << (result ? "pass" : "fail") << endl;

    result = matrix_find(matrix[0], 4, 4, 15);
    cout << "3: " << (result ? "pass" : "fail") << endl;

    result = matrix_find(matrix[0], 4, 4, 3);
    cout << "4: " << (!result ? "pass" : "fail") << endl;

    result = matrix_find(matrix[0], 4, 4, 0);
    cout << "5: " << (!result ? "pass" : "fail") << endl;

    result = matrix_find(matrix[0], 4, 4, 16);
    cout << "6: " << (!result ? "pass" : "fail") << endl;

    result = matrix_find(nullptr, 4, 4, 7);
    cout << "7: " << (!result ? "pass" : "fail") << endl;

    result = matrix_find(matrix[0], -1, -1, 7);
    cout << "8: " << (!result ? "pass" : "fail") << endl;
}
```
