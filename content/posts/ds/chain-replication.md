---
title: "Chain Replication"
date: 2022-11-18T15:04:55+08:00
tags: []
categories: ["Distributed System"]
summary: "Chain replication, server crash, server join and split object across many chains."
---

## Chain Replication

![Chain Replication Example](https://oos.axlis.cn/blog/ds/3.png)

* Client RPC split between head and tail.
* Head only send update RPC to the next server.
* Read operation only involve tail.

## Crash

* Head: change head to next.
* Tail: change tail to prev.
* Intermediate: remove it and prev need to transfer missing updates to the next.

## Join

Copy tail and switch tail.

## Split Object Across Many Chains

![Many Chains Example](https://oos.axlis.cn/blog/ds/4.png)
