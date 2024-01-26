---
title: "ElasticSearch - 实体之间的关系"
date: 2024-01-25T10:00:00+08:00
categories: ["ElasticSearch"]
summary: "对象模型、一对多解决方案、多对多解决方案"
---

# 思维导图

![Relation](https://dl.axlis.cn/blog/es/relation.png)

> [下载Xmind文件](https://dl.axlis.cn/blog/es/relation.xmind)

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

如果将内部的对象的类型定义为`nested`, 则 ElasticSearch 会将内部对象单独索引, 也就可以保证内部对象的边界不丢失.

为了查询性能, ElasticSearch 将`nested`类型的内部对象和根对象存储到一个块(Block).

但如果内部对象频繁的变更, 会导致整个块(Block)一起更新, 从而影响更新的性能.

## Parent

如果声明了`parent`, 通过 ID 的方式维护引用, 避免了更新内部对象影响性能的问题.

并且 ElasticSearch 保证`parent`和`child`都会被路由到一个节点上, 避免了分布式 JOIN 的问题.

# 多对多解决方案

## 冗余

冗余当然是一种反范式设计, 其目的是为了避免分布式 JOIN 的问题, 当然本质上还是在解决查询性能的问题.

当然在更新这一方面, 它的成本会高一些, 因为所用冗余的数据都需要进行同步的更改.

另外部分查询还有可能会返回冗余数据, 甚至还会影响算分的过程, 需要进行一些额外的去重工作.

## 应用层 JOIN

如果你不关注查询性能, 你可以通过 ID 的方式维护引用, 并在应用层进行多次查询, 最后自己 JOIN 数据.
