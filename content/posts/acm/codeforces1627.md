---
title: "Codeforces Round #766 (Div. 2)"
date: 2022-01-18T22:08:05+08:00
tags: ["Codeforces"]
categories: ["ACM"]
summary: "日常比赛"
---

## [A - Not Shading](https://codeforces.com/contest/1627/problem/A)

```c++
void solve() {
    int    n,   m,   row,   col;
    cin >> n >> m >> row >> col;

    row--;
    col--;

    vector<vector<char>> grid(n, vector<char>(m));
    bool has_black = false;
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            cin >> grid[i][j];
            if (!has_black && grid[i][j] == 'B') has_black = true;
        }
    }

    if (!has_black) {
        return cout << -1 << endl, void();
    }

    if (grid[row][col] == 'B') {
        return cout << 0 << endl, void();
    }

    for (int i = 0; i < n; i++) {
        if (grid[i][col] == 'B') {
            return cout << 1 << endl, void();
        }
    }

    for (int i = 0; i < m; i++) {
        if (grid[row][i] == 'B') {
            return cout << 1 << endl, void();
        }
    }

    cout << 2 << endl;
}
```

## [B - Not Sitting](https://codeforces.com/contest/1627/problem/B)

最远的做法就是坐在角落, 而首先涂掉的就是那些离角落很近的地方; 统计每个地方的距离, 在进行排序就好了.

```c++
void solve() {
    int    n,   m;
    cin >> n >> m;

    vector<int> res;

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            res.push_back(max(i, n - i - 1) + max(j, m - j - 1));
        }
    }

    sort(res.begin(), res.end());

    for (int i = 0; i < res.size(); i++) {
        cout << res[i] << ' ';
    }

    cout << endl;
}
```

## [C - Not Assigning](https://codeforces.com/contest/1627/problem/C)

核心理论: 在所有质数中, 仅有2既是偶数又是质数; 而恰好两个奇质数之和就是偶数.

经过简单的分析, 我们发现这棵树没有度超过3的节点并且恰好连成一条直线.

```c++
struct Link {
    int edge;
    int tail;
};

void solve() {
    int    n;
    cin >> n;

    vector<vector<Link>> G(n);

    bool fail = false;
    for (int i = 0; i < n - 1; i++) {
        int    x,   y;
        cin >> x >> y;

        if (fail) continue;

        x--;
        y--;

        if (G[x].size() < 2 && G[y].size() < 2) {
            G[x].push_back(Link{.edge = i, .tail = y});
            G[y].push_back(Link{.edge = i, .tail = x});
        } else fail = true; // 标记为不可能.
    }

    if (fail) return cout << -1 << endl, void();

    int head = -1, tail = -1;

    // 找出头尾.
    for (int i = 0; i < n; i++) {
        if (G[i].size() != 1) continue;
        if (head == -1) {
            head = i;
            continue;
        }
        if (tail == -1) {
            tail = i;
            continue;
        }
        throw runtime_error("@1: two much leaves");
    }

    vector<int> weight(n - 1);

    // 迭代填入结果.
    int prime = 2, pre = -1;
    while (head != tail) {
        for (auto &link : G[head]) {
            if (link.tail != pre) {
                weight[link.edge] = prime;
                prime = (prime == 2) ? 3 : 2;
                pre   = head;
                head  = link.tail;
                break;
            }
        }
    }

    for (int i = 0; i < n - 1; i++) {
        cout << weight[i] << ' ';
    }
    cout << endl;
}
```