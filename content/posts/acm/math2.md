---
title: "除余"
date: 2022-01-19T22:49:30+08:00
tags: ["Math"]
categories: ["ACM"]
summary: "除法, 求余, 因数, 倍数等"
---

## 问题介绍(一)

给定一个n, 求出$\sum_{i=1}^n \lfloor \frac{n}{i} \rfloor, n \leq 10^9$的值.

## 问题解答(一)

对于$\lfloor \frac{n}{i} \rfloor$, 它就是需要被求和的`val`, 同时表示n可以完全容纳下`i * val`.

那么n最大可以容纳下多少个`val`呢? 显然是`n / val`. 没错这就是我们要求的上界!

```c++
long long sum(int n) {
    long long res = 0;

    for (int i = 1, j; i <= n; i = j + 1) {
        int val = n / i;
        j       = n / val;

        res += (j - i + 1) * val;
    }

    return res;
}
```

在几次实验中, 该算法耗时最长为0.001s, 而直接迭代耗时最长为4.041s.

## [问题介绍(二)](https://codeforces.com/contest/1542/problem/C)

定义: f(i) = 最小的不能整除i的数; 求 $\sum_{i=1}^n f(i) \mod (1e8+7)$.

## [问题解答(二)](https://codeforces.com/contest/1542/submission/143488966)

![2-1](https://axlis.oss-cn-hangzhou.aliyuncs.com/blog/acm/math/2-1.jpg)

```c++
const int LIMIT = 42;
const int MOD   = 1e9 + 7;

long long gcd(long long x, long long y) {
    if (y == 0) return x;
    return gcd(y, x % y);
}

long long lcm(long long x, long long y) {
    return x * y / gcd(x, y);
}

void solve(vector<long long> &table) {
    long long n, res = 0;
    cin >> n;

    for (int i = 2; i < LIMIT; i++) {
        res += (n / table[i-1] - n / table[i]) * i;
    }

    cout << res % MOD << endl;
}

int main() {
    vector<long long> table(LIMIT, 1);
    for (int i = 2; i < LIMIT; i++) {
        table[i] = lcm(table[i-1], i);
    }

    int    cases;
    cin >> cases;
    for (int i = 0; i < cases; i++) solve(table);
}

```