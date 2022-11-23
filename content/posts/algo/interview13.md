---
title: "13-机器人运动范围"
date: 2022-02-10T15:39:13+08:00
tags: ["interview"]
categories: ["Algorithm"]
summary: "剑指Offer面试题13的归纳"
---

## 问题介绍

机器人从坐标(0, 0)处出发, 在方格中上下左右移动, 但坐标数位和必须小于等于特定数.

```c++
int digitalSum(int num) {
    int digit = 0;

    while (num != 0) {
        digit = digit + num % 10;
        num = num / 10;
    }

    return digit;
}

int reachCountCore(bool *visited, int rows, int cols, int row, int col, int maxSum) {
    int counts = 0;

    if (row < 0 || row >= rows || col < 0 || col >= cols || visited[row * cols + col]) {
        return counts; // 越界和已访问
    }

    visited[row * cols + col] = true;

    if (digitalSum(row) + digitalSum(col) > maxSum) {
        return counts; // 数位和不满足
    }

    counts += reachCountCore(visited, rows, cols, row + 1, col, maxSum);
    counts += reachCountCore(visited, rows, cols, row - 1, col, maxSum);
    counts += reachCountCore(visited, rows, cols, row, col + 1, maxSum);
    counts += reachCountCore(visited, rows, cols, row, col - 1, maxSum);

    return counts + 1;
}

int reachCount(int rows, int cols, int maxSum) {
    if (rows <= 0 || cols <= 0) return 0;

    bool *visited = new bool[rows * cols];

    for (int i = 0; i < rows * cols; i++) {
        visited[i] = false;
    }

    int counts = reachCountCore(visited, rows, cols, 0, 0, maxSum);

    delete[] visited;

    return counts;
}

void test(const string &name, int rows, int cols, int maxSum, int expected) {
    if (!name.empty()) {
        cout << name << " start: ";
    }

    if (reachCount(rows, cols, maxSum) == expected) {
        cout << "PASS" << endl;
    } else {
        cout << "FAIL" << endl;
    }
}

void test1() {
    test("TEST(1)", 20, 20, 15, 359);
}

void test2() {
    test("TEST(2)", 1, 100, 10, 29);
}

void test3() {
    test("TEST(3)", 100, 1, 15, 79);
}

void test4() {
    test("TEST(4)", -5, -5, 10, 0);
}
```