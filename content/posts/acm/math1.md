---
title: "质数"
date: 2022-01-08T18:05:12+08:00
tags: ["Math"]
categories: ["ACM"]
summary: "基本质数知识和典型例题"
---

## 质数的定义

对于任何大于1的数, 如果除了1和它本身外, 不能被其他自然数整除, 那么它就是质数(Prime Number).

## 问题介绍(一)

已知正整数n是两个不同的质数的乘积, 试求出较大的那个质数.

## 问题解答(一)

```c++
int main() {
    int    num;
    cin >> num;

    int bound = (int)sqrt(num);
    
    // 寻找较小的质数.
    for (int i = 2; i <= bound; i++) {
        if (num % i == 0) {
            cout << num / i;
            break;
        }
    }
}
```

解决问题时, 我们利用性质: 如果一个数是两个质数的乘积, 那么这两个质数就必定是唯一的.

## 欧拉筛

![1-1](https://dl.axlis.cn/blog/acm/math/1-1.svg)

```c++
vector<int> euler_prime(int bound) {
    // 没有小于2的素数.
    if (bound < 2) throw runtime_error("invalid bound");

    // 索引对应数, 范围为[2, bound].
    vector<bool> visited(bound + 1);
    vector<int>  primes;

    for (int num = 2; num <= bound; num++) {
        if (!visited[num]) primes.push_back(num);

        for (int prime : primes) {
            int exclude = prime * num;
            // 超出区间范围.
            if (exclude > bound) break;
            // 排除质数的可能性.
            visited[exclude] = true;
            // 直到分解后最低位指数递增.
            if (num % prime == 0) break;
        }
    }

    return primes;
}
```