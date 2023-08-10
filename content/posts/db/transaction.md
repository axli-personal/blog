---
title: "Transaction"
date: 2022-11-19T13:50:03+08:00
tags: []
categories: ["Database"]
summary: "Concurrency Problems, Transaction Properties, Two Phase Locking, Lock Management, Deadlock and Multiple Granularity Locking."
---

## Concurrency Problems

* Inconsistent Reads: A user reads only part of what was updated.
* Lost Update: Two users try to update the same record at the same time so one of the updates gets lost.
* Dirty Reads: One user reads an update that was never committed.

## Transaction Properties

* Atomicity: commit or abort.
* Consistency: abort when integrity constraints are violated.
* Isolation: concurrency.
* Durabilty: fault tolerance.

## Two Phase Locking

* Must acquire a shared lock before reading, and an exclusive lock before writing.
* Cannot acquire new locks after releasing any locks.

> Strict Two Phase Locking: locks get released when the transaction commit (prevent cascading aborts).

## Lock Management

If queue skipping is not allowed:

```python
H = held_locks
Q = request_queue

def request(lock_request):
    if empty(Q) and compatible(lock_request, H):
        grant(lock_request)
    else:
        addToQueue(lock_request)

def release_procedure(lock_to_release):
    release(lock_to_release)
    for lock_request in Q:
        if compatible(lock_request, H):
            grant(lock_request)
        else:
            return
```

If queue skipping is allowed:

```python
H = held_locks
Q = request_queue

def request(lock_request):
    if compatible(lock_request, H):
        grant(lock_request)
    else:
        addToQueue(lock_request)

def release_procedure(lock_to_release):
    release(lock_to_release)
    for lock_request in Q:
        if compatible(lock_request, H):
            grant(lock_request)
```

> Put lock upgrade request at the front of the queue.

## Deadlock

### Avoidance

Transaction priority: now - start time.

* Wait-Die: if transaction has higher priority, then waits, else aborts.
* Wound-Wait: if transaction has higher priority, then wounds; else aborts.

> If a transaction restarts, priority = now - original start time.

### Detection

Detect deadlocks by maintaining a 'waits-for' graph.

If a cycle is found, we will abort a transaction to break the cycle.

## Multiple Granularity Locking

### Lock Modes

* S: Shared lock.
* X: Exclusive lock.
* IS: Intent to get shared lock at finer granularity.
* IX: Intent to get exclusive lock at finer granularity.
* SIX: S + IX.

### Compatibility Matrix

![Compatibility Matrix](https://dl.axlis.cn/blog/db/1.png)

### Rules

* Each transaction starts from the root of the hierarchy.
* To get S or IS lock on a node, must hold IS or IX on parent node.
* To get X or IX on a node, must hold IX or SIX on parent node.
* Must release locks in bottom-up order.
