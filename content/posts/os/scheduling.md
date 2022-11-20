---
title: "Scheduling"
date: 2022-09-23T20:43:24+08:00
tags: []
categories: ["OS"]
summary: "Linux Process Scheduler."
---

## The Linux Implementation of Threads

Linux implements all threads as standard processes and windows has explicit kernel support for threads.

In Linux, a thread is merely a process that shares certain resources (specified by flags) with other processes.

## Traditional UNIX scheduler

The priority is exported to user space in the form of nice values, but it leads to several problems.

* Low-priority processes switch very frequently.
* The same priority change will have more effects on lower priorities.
* Timeslice must be some integer multiple of the timer tick.
* Tend to provide interactive processes with more processor time.

## Preempt task running in the kernel

The kernel can preempt a task running in the kernel so long as it does not hold a lock.

So we need to add a **preemption counter** to each process.

The preemption counter increase when lock is acquired and decrease when lock is released.

And the kernel is preemptible when the preemption counter is 0.