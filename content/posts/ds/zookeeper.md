---
title: "ZooKeeper"
date: 2022-11-18T09:14:58+08:00
tags: []
categories: ["Distributed System"]
summary: "ZooKeeper's performance, correctness definition and programming model."
---

## ZooKeeper Performance

![Performance Statistic](https://dl.axlis.cn/blog/ds/2.png)

## Different Correctness Definition

* Write: linearizable.
* Read: observe last write from the same client (maintain log index returned by the last write).

## Different Programming Model

### Watch

![Watch Example](https://dl.axlis.cn/blog/ds/1.png)

### Version

```python
def inc():
    while True:
        val, version = getData("cnt")
        if setData("cnt", val + 1, version):
            break
```

If the `version` is the same, `setData` will success.

This rule prevents you from interleaving `getData` and `setData`.

### 分布式锁

```python
def acquire():
    lock = create(directory + "lock-", ephemeral|sequential)
    while True:
        children = getChildren(directory)
        if isLowest(lock, children):
            break
        # 监听上一个节点, 防止惊群效应.
        if exist(prevPath(lock, children), watch=True):
            waitForNotification()

def release():
    delete("lock")
```

If lock holder fail, system will enter intermediate state (`ephemeral` flag prevent deadlock).

So system must clean up the intermediate state or accept retry.

## 注册中心

ZooKeeper作为注册中心提供的功能:

* 订阅: 当ZK数据节点变更时, 客户端能够收到通知.
* 心跳: 当ZK临时节点心跳丢失后, 会被自动删除.

ZooKeeper作为注册中心的不足:

* 当出现网络分区时, 少数派无法提供服务.
* 写请求只能由Leader完成, 在服务同时上下线时, Leader压力会非常大.
