---
title: "Codeforces Contest 1641"
date: 2022-03-05T12:52:00+08:00
tags: ["Codeforces"]
categories: ["Algorithm"]
summary: "solution for codeforces contest 1641"
---

## A - Great Sequence

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <map>

using namespace std;

void solve() {
    int    n,   x;
    cin >> n >> x;

    vector<long long>   nums(n);
    map<long long, int> freq; // occurrence frequency.

    for (int i = 0; i < n; i++) {
        cin >> nums[i];
        freq[nums[i]]++;
    }

    sort(nums.begin(), nums.end());

    int need = 0;

    for (int i = 0; i < n; i++) {
        if (freq[nums[i]] == 0) continue;

        if (freq[nums[i] * x] == 0) {
            need++; // Need to append one to the sequence.
        } else {
            freq[nums[i] * x]--;
        }

        freq[nums[i]]--;
    }

    cout << need << endl;
}

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    int    cases;
    cin >> cases;
    while (cases-- > 0) solve();
}
```

Note: using `unordered_map` will get TLE due to hash collision.