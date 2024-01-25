---
title: "SQL - 2023/01/24 面试题回顾"
date: 2024-01-25T07:00:00+08:00
categories: ["SQL"]
summary: "题目描述、初步尝试、进一步尝试"
---

# 题目描述

现有用户表(users)、用户访问图书记录表(visits), 如果我有100个用户, 我需要查这些用户当日最后一次访问记录.

使用`MAX`函数拿最大的`visits.id`不是我想要的, 我需要知道那条完整的记录是什么.

# 初步尝试

我有一段时间没有写过SQL代码了, 一开始的思路是先`JOIN`后`GROUP BY`, 于是我写下了如下代码:

```sql
SELECT *
FROM users
         JOIN visits
WHERE users.id = visits.user_id
GROUP BY users.id;
```

写到这里我就向面试官复述了一下题目, 以确定我对题目的理解是正确的.

## 进一步尝试

```sql
SELECT *
FROM (SELECT users.id, MAX(visits.ts)
      FROM users
               JOIN visits
      WHERE users.id = visits.user_id
      GROUP BY users.id) AS user_latest_visit
JOIN visits;
```

由于我关注到了拿不到`visits`表整行的数据, 所以又用了一个查询去获取这部分数据.

写到这里我与面试官讲述了我解决该问题的思路, 面试官讲述了它的看法, 并提到解决问题时需要抓住本质.

## 更优的解法

由于面试时时间有限, 并没有将问题具体化, 仅仅讨论了一下思路, 可能最终写出来的代码也不是最优的.
