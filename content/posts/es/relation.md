---
title: "ElasticSearch - 实体之间的关系"
date: 2024-01-25T10:00:00+08:00
categories: ["ElasticSearch"]
summary: "草稿，预计2024/01/26前完成"
---

# 对象模型

ES 看起来和 MongoDB 类似, 都使用对象作为存储模型, 但其底层的索引方式有很大的区别.

如果使用 ES 存储以下文档:

```json
{
  "name": "小明(用户)",
  "blogs": [
    {
      "date": "2024-01-01",
      "title": "文章A"
    },
    {
      "date": "2024-01-05",
      "title": "文章B"
    }
  ]
}
```

底层的索引方式其实是铺平的:

```json
{
  "name": "小明(用户)",
  "blogs.date": ["2024-01-01", "2024-01-05"],
  "blogs.title": ["文章A", "文章B"]
}
```

这样索引后 blogs 数组中的对象边界就丢失了:

```json
{
  "bool": {
    "must": [
      {
        "term": {
          "blogs.title": "文章A"
        }
      },
      {
        "range": {
          "blogs.date": {
            "from": "2024-01-04"
          }
        }
      }
    ]
  }
}
```

以上的查询会返回`小明`这个文档, 即使`文章A`的时间并不满足条件.

# 一对多解决方案

## Nested Type

## Parent

# 多对多解决方案

## 冗余

## 应用层 JOIN
