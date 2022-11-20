---
title: "Query Optimization"
date: 2022-11-07T12:00:00+08:00
categories: ["Database"]
summary: "Query plan space and query optimization."
---

## Plan Space

There are too many query plans for a complex query.

We need to cut down the number of plans:

* Push down projects and selects as far as they can go.
* Left deep join.

## Optimization

There are 2 option to access each single table:

* Full Scan.
* Index Scan.

There are many option to perform left deep join:

* Nested Loop Join (some algorithms preserve the order of the left relation).
* Hash Join.
* Sort Merge Join (sorted on columns in the join condition).

Materialize can reduce IO costs in many cases.
