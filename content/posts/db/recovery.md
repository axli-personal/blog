---
title: "Recovery"
date: 2023-01-29T19:58:25+08:00
tags: []
categories: ["Database"]
summary: "Write Ahead Logging helps database recover from failure."
---

## Buffer Management Policy

* No Force: Only write back to disk when the page needs to be evicted from the buffer pool.
* Steal: Allows modified pages to be written to disk before a transaction finishes.

## Write Ahead Logging

Rule:

* Log records must be written to disk before the corresponding data page gets written to disk.
* All log records must be written to disk when a transaction commits.

Implementation:

* LSN: log sequence number.
* Previous LSN: last operation from the same transaction.
* Flushed LSN: the LSN of last log record that has been flushed to disk.
* Page LSN: LSN of the operation that last modified the page.

## Recovery Process

* Analysis: reconstruct transaction table, dirty page table.
* Redo: ensure durability.
* Undo: ensure atomicity.
