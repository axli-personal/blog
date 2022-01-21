---
title: "线性动态规划"
date: 2022-01-20T11:59:38+08:00
tags: ["DP"]
categories: ["Math"]
---

## [问题介绍](https://www.luogu.com.cn/problem/P2758)

通过下列操作将给定字符串转换为目标字符串:

* 删除一个字符.
* 增加一个字符.
* 修改一个字符.

## 问题解答

```c++
const int MAX_TIMES = 2000;

void solve() {
    string a,   b;
    cin >> a >> b;

    vector<vector<int>> dp(a.size() + 1, vector<int>(b.size() + 1, MAX_TIMES));

    dp[0][0] = 0;

    for (int i = 0; i <= a.size(); i++) {
        for (int j = 0; j <= b.size(); j++) {
            if (i + 1 <= a.size()) {
                dp[i+1][j] = min(dp[i+1][j], dp[i][j] + 1);
            }
            if (j + 1 <= b.size()) {
                dp[i][j+1] = min(dp[i][j+1], dp[i][j] + 1);
            }
            if (i + 1 <= a.size() && j + 1 <= b.size()) {
                dp[i+1][j+1] = dp[i][j] + (a[i] == b[j] ? 0 : 1);
            }
        }
    }

    cout << dp.back().back() << endl;
}
```

