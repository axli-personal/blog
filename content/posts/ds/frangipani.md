---
title: "Frangipani"
date: 2022-11-21T10:53:37+08:00
tags: []
categories: ["Distributed System"]
summary: "Frangipani's structure, use case, cache consistency and recovery."
---

## Structure

![Structure](https://oos.axlis.cn/blog/ds/5.png)

## Use Case

* Trust enviroment.
* Users mostly work on their private files.
* Users potentialy share files with each other.

## Cache Consistency

Lock table (lock service):

| file  | owner |
| ----- | ----- |
| file1 | user1 |
| file2 | user1 |

Lock table (user1):

| file  | state |
| ----- | ----- |
| file1 | busy  |
| file2 | idle  |

![Example](https://oos.axlis.cn/blog/ds/6.png)

## Recovery

![Many Logs](https://oos.axlis.cn/blog/ds/7.png)

> Only replay update records with current version of the block.
