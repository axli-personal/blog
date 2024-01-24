---
title: "LeetCode 853 - Car Fleet"
date: 2024-01-24T10:00:00+08:00
categories: ["Algorithm"]
summary: "题目介绍、初步分析、深入分析"
---

# 题目介绍

在一条单行道上，有 `n` 辆车开往同一目的地。目的地是几英里以外的 `target` 。

给定两个整数数组 `position` 和 `speed` ，长度都是 `n` ，其中 `position[i]` 是第 `i` 辆车的位置， `speed[i]` 是第 `i` 辆车的速度(单位是英里/小时)。

一辆车永远不会超过前面的另一辆车，但它可以追上去，并与前车 **以相同的速度** 紧接着行驶。此时，我们会忽略这两辆车之间的距离，也就是说，它们被假定处于相同的位置。

**车队** 是一些由行驶在相同位置、具有相同速度的车组成的非空集合。注意，一辆车也可以是一个车队。

即便一辆车在目的地才赶上了一个车队，它们仍然会被视作是同一个车队。

返回到达目的地的 **车队数量** 。

> [LeetCode 853 题目链接](https://leetcode.cn/problems/car-fleet/)

# 初步分析

如果一辆车**到达终点的时间**小于前面的一辆车, 则它们两个就能够形成一个车队.

那么就会形成以下思路:

1. 按照车的位置排序.
2. 倒序遍历数组, 检查每个车是否能够和前一个车形成一个车队.

于是有了以下代码:

```java
class Solution {
    static class Car {
        public int position;

        public int speed;

        public Car(int position, int speed) {
            this.position = position;
            this.speed = speed;
        }
    }

    public int carFleet(int target, int[] position, int[] speed) {
        List<Car> cars = new ArrayList<>();
        for (int i = 0; i < position.length; i++) {
            cars.add(new Car(position[i], speed[i]));
        }

        cars.sort((a, b) -> (a.position != b.position) ? a.position - b.position : a.speed - b.speed);

        if (cars.isEmpty()) {
            return 0;
        }

        int fleetCount = 1; // 一个车队, 最后一辆车.
        for (int i = cars.size() - 2; i >= 0; i--) {
            // 除法比较改为乘法.
            if ((long) (target - cars.get(i).position) * cars.get(i + 1).speed >
                    (long) (target - cars.get(i + 1).position) * cars.get(i).speed) {
                fleetCount++; // 到达终点的时间晚于前车.
            }
        }

        return fleetCount;
    }
}
```

# 深入分析

上面的代码只能通过部分的用例, 原因是'**检查每个车是否能够和前一个车形成一个车队**'的判断可能出现问题.

如果**前车和更前面的车形成车队**, 那么前车的速度将不是线性的, 导致我们的比较不正确.

虽然前车的速度不是线性的不好比较, 但是前车车队中总有一个车的速度是线性的, 没错车队中领头冲过终点的一辆.

所以我们只需要**记录一下前一个车队的长度**, 就可以找到领头的车了, 于是有了如下的最终代码:

```java
class Solution {
    static class Car {
        public int position;

        public int speed;

        public Car(int position, int speed) {
            this.position = position;
            this.speed = speed;
        }
    }

    public int carFleet(int target, int[] position, int[] speed) {
        List<Car> cars = new ArrayList<>();
        for (int i = 0; i < position.length; i++) {
            cars.add(new Car(position[i], speed[i]));
        }

        cars.sort((a, b) -> (a.position != b.position) ? a.position - b.position : a.speed - b.speed);

        if (cars.isEmpty()) {
            return 0;
        }

        int fleetCount = 1; // 一个车队, 最后一辆车.
        int fleetCarCount = 1; // 车队一开始只有一辆车
        for (int i = cars.size() - 2; i >= 0; i--) {
            // 除法比较改为乘法.
            if ((long) (target - cars.get(i).position) * cars.get(i + fleetCarCount).speed >
                    (long) (target - cars.get(i + fleetCarCount).position) * cars.get(i).speed) {
                fleetCount++; // 到达终点的时间晚于前车.
                fleetCarCount = 0; // 旧车队已经不用考虑了.
            }

            fleetCarCount++;
        }

        return fleetCount;
    }
}
```

# 参考资料

1. [NeetCode - Leetcode 853](https://www.youtube.com/watch?v=Pr6T-3yB9RM)
