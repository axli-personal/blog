---
title: "LeetCode - Day 1"
date: 2022-11-23T17:05:17+08:00
tags: []
categories: ["Algorithm"]
summary: "Container With Most Water, 3Sum, Longest Palindromic Substring and Remove Nth Node From End of List."
---

## A - Container With Most Water

### Brute Force

Time Complexity: $\Theta(n^2)$.

### Two Pointer

you move one pointer, becasuse move another pointer will definatly get a non-optimal solution.

You move L, because $Result(L, R_{new}), R_{new} < R$ are non-optimal solutions.

> You don't need to consider $Result(L_{new}, R_{new}), L_{new} > L, R_{new} < R$, because move L will get those results.

In this question, move the pointer with higher height will definatly get a non-optimal solution.

Time Complexity: $\Theta(n^2)$.

## B - 3Sum

### Sort & Two Pointer

Preprocess: sort.

If $num_i <= 0$, find $num_j + num_k = -num_i, i < j < k$.

Subproblems are two sum problems that can be solved by two pointer technique.

## C - Longest Palindromic Substring

### Expand Around Center

## D - Remove Nth Node From End of List

### Two Pointer

Use dummy node to handle edge cases.
