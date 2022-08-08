---
title: "Convex Hull"
date: 2022-08-06T15:00:00+08:00
categories: ["Algorithm"]
---

Convex hull is a geometric problems which can be solved by divide and conquer.

<!--more-->

## Problem

CH(S): smallest polygon containing all points in S.

## Solution

Preprocess: Sort points by x.

Divide: left half A and right half B.

Conquer: CH(A), CH(B).

Combine: find upper and lower tangent by clockwise or counterclockwise.

## Efficiency

T(n) = 2T($\frac{n}{2}$) + $\Theta(n)$ = $\Theta(n \log n)$

## Reference

Introduction to algorithms - Chapter 33