---
title: "Leetcode2"
date: 2022-11-24T16:16:48+08:00
tags: []
categories: []
summary: "TODO"
---

## A - Permutations

### Backtracking

Record current used numbers.

Time Complexity: $\Theta(n * n!)$.

### Next Permutation

From right to left, find `nums[i] < nums[i + 1]`.

From right to left, find `nums[j] > nums[i]`.

Then, `swap(i, j)` and `reverse(i + 1, length - 1)`.

Time Complexity: $\Omicron(n)$.

## B - Combination Sum

### Backtracking

### Dynamic Programming

## C - Find First and Last Position of Element in Sorted Array

### Binary Search

## D - Search in Rotated Sorted Array

### Binary Search
