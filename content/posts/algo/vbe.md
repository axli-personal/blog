---
title: "van Emde Boas tree"
date: 2022-08-06T10:00:00+08:00
categories: ["Algorithm"]
---

The vBE(van Emde Boas) tree can deal with successor and predecessor problem in $\Theta(\log\log u)$ time.

But the vEB tree has poor space efficiency, which makes it impractical for most applications.

<!--more-->

## Structure

u: size of the universe.

min: min number in the universe (lazy).

max: max number in the universe (recursive).

cluster: $\sqrt{u}$ sub trees of size $\sqrt{u}$ to store number.

summary: tree of size $\sqrt{u}$ to record whether corresponding sub tree contains number.

## Operation

Insert, Delete, Successor and Predecessor: $\Theta(\log\log u)$.

> Time efficiency comes from: T(u) = T($\sqrt{u}$) + O(1).

## Reference

Introduction to algorithms - Chapter 20