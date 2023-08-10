---
title: "FaRM"
date: 2022-11-25T10:15:43+08:00
tags: []
categories: ["Distributed System"]
summary: "FaRM's Background, Feature, Structure and Optimistic Concurrency Control."
---

## Background

* One data center.
* Use memory instead of disk to store data.

## Feature

* Fast.
* Serializability.

## Structure

![Structure](https://dl.axlis.cn/blog/ds/8.png)

## Optimistic Concurrency Control

* Read objects without lock.
* Write objects with lock.
* Check version before commit.
