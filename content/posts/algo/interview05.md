---
title: "5-替换空格"
date: 2022-01-07T22:02:00+08:00
tags: ["interview"]
categories: ["algorithm"]
summary: "剑指Offer面试题5的归纳"
---

## 问题介绍

需要将给定字符串进行URL编码, 但只需要编码空格.

## 尝试解答

通过预先计算分配的内存可以很自然的写出$T = O(n)$的解法.

```c++
/**
  This function can replace ' ' to '%20'
  in a c-style string end with '\0'.

  User must delete the return pointer,
  even the string doesn't contain ' '.

  Pass in nullptr will not raise an error,
  instead return a special value nullptr.
*/
char* replace_whitespace(const char* str) {
    if (str == nullptr) return nullptr;

    int pos, whitespaces = 0;
    for (pos = 0; str[pos] != '\0'; pos++) {
        if (str[pos] == ' ') whitespaces++;
    }

    auto res = new char[pos + 2 * whitespaces + 1];
    for (int i = 0, j = 0; i < pos; i++) {
        if (str[i] == ' ') {
            res[j++] = '%';
            res[j++] = '2';
            res[j++] = '0';
        }
        else res[j++] = str[i];
    }
    res[pos + 2 * whitespaces] = '\0';

    return res;
}

void test() {
    const char* cases[]{
        "We are happy",
        "Hello  world",
        " Begin",
        "End ",
        "Normal",
        "",
        " ",
        "  "
    };
    const char* ans[]{
        "We%20are%20happy",
        "Hello%20%20world",
        "%20Begin",
        "End%20",
        "Normal",
        "",
        "%20",
        "%20%20"
    };

    for (int i = 0; i < 8; i++) {
        const char* ret = replace_whitespace(cases[i]);
        cout << i + 1 << ": " << (!strcmp(ret, ans[i]) ? "pass" : "fail") << endl;
        delete ret;
    }
    cout << "9: " << (!replace_whitespace(nullptr) ? "pass" : "fail") << endl;
}
```

海涛提到我们需要问清楚实在原字符串实现, 还是新开辟字符串实现, 而我默认选择了新开辟.

而在一番分析后, 发现好像在原字符串上实现更有挑战, 用到了从后向前双指针填充, 十分巧妙.

海涛在实现原字符串上的实现对, 在预留长度不足时选择了直接返回, 我更倾向通知用户.
