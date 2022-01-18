---
title: "Codeforces Round #765 (Div. 2)"
date: 2022-01-15T19:18:46+08:00
tags: ["Codeforces"]
categories: ["ACM"]
summary: "日常比赛"
---

## [A - Ancient Civilization](https://codeforces.com/contest/1625/problem/A)

每个单词有很多种可能形式, 我们只需要统计每个位置个字母出现的频率; 由于单词表示成二进制的形式, 那就需要用到一点二进制的知识.

```c++
void solve() {
    int    n,   len;
    cin >> n >> len;

    vector<int> collector(len);

    for (int i = 0; i < n; i++) {
        int    word;
        cin >> word;

        for (int j = 0; j < len; j++) {
            collector[j] += ((1 << j) & word) >> j;
        }
    }

    int res = 0;
    for (int j = 0; j < len; j++) {
        if (collector[j] * 2 > n) {
            res |= 1 << j;
        }
    }

    cout << res << endl;
}
```

## [B - Elementary Particles](https://codeforces.com/contest/1625/problem/B)

对于能重叠的部分一定在某个相同位置有相同元素, 观察得出: $res = n - closest$.

```c++
void solve() {
    int    n;
    cin >> n;

    unordered_map<int, int> record;
    int                     closest = n;

    for (int i = 0; i < n; i++) {
        int    num;
        cin >> num;

        auto it = record.find(num);
        if (it != record.end()) {
            closest = min(closest, i - it->second);
        }
        record[num] = i;
    }

    if (closest == n) cout << -1 << endl;
    else              cout << n - closest << endl;
}
```

## [C - Road Optimization](https://codeforces.com/contest/1625/problem/C)

移除限速牌一看就需要使用动态规划(Dynamic Programming).

一开始路上只有开始标志牌, 然后我们选择下一个标志牌, 并把两者中间未选择的标志牌移除, 所以不移除任何标志牌就是动态规划的初始条件.

```c++
void solve() {
    const int MAX_TIME = 1e9; // 上界.

    int    signs,   distance,   remove;
    cin >> signs >> distance >> remove;

    vector<int> pos(signs + 1, distance), limit(signs); // 注意终点的位置.

    for (int i = 0; i < signs; i++) cin >> pos[i];
    for (int i = 0; i < signs; i++) cin >> limit[i];

    vector<vector<int>> dp(signs + 1, vector<int>(remove + 1, MAX_TIME));

    dp[0][0] = 0;
    for (int i = 1; i <= signs; i++) dp[i][0] = dp[i-1][0] + limit[i-1] * (pos[i] - pos[i-1]);

    for (int i = 0; i < signs; i++) {
        for (int j = 0; j <= remove; j++) {
            // 不可达.
            if (dp[i][j] == MAX_TIME) continue;
            for (int k = i + 1; k <= signs; k++) {
                if (j + k - i -1 > remove) break;
                // 添加下一个标志牌.
                dp[k][j+k-i-1] = min(dp[k][j+k-i-1], dp[i][j] + limit[i] * (pos[k] - pos[i]));
            }
        }
    }

    int res = MAX_TIME;

    for (int i = 0; i <= remove; i++) {
        res = min(res, dp[signs][i]);
    }

    cout << res << endl;
}
```