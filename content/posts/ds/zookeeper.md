---
title: "ZooKeeper"
date: 2022-11-18T09:14:58+08:00
tags: []
categories: ["Distributed System"]
summary: "ZooKeeper's performance, correctness definition and programming model."
---

## ZooKeeper Performance

![Performance Statistic](https://oos.axlis.cn/blog/ds/2.png)

## Different Correctness Definition

* Write: linearizable.
* Read: observe last write from the same client (maintain log index returned by the last write).

## Different Programming Model

### Watch

![Watch Example](https://oos.axlis.cn/blog/ds/1.png)

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

### Lock

```python
def acquire():
    lock = create(directory + "lock-", ephemeral|sequential)
    while True:
        children = getChildren(directory)
        if isLowest(lock, children):
            break
        if exist(prevPath(lock, children), watch=True):
            waitForNotification()

def release():
    delete("lock")
```

If lock holder fail, system will enter intermediate state (`ephemeral` flag prevent deadlock).

So system must clean up the intermediate state or accept retry.
