---
title: "Spanner"
date: 2022-11-22T16:59:19+08:00
tags: []
categories: ["Distributed System"]
summary: "TODO"
---

## Spanner

* Real System, Google Cloud Product.
* Vide area transaction.

Read only: snapshot + isolation + sync clock.

## High Level

Mutiple shards -> performance.

Raft per shard, majority rule:

fault torlence, slowness.

Replica close to client.

## Challenages

* Read of local replica yield latest data.
* Transaction cross shard.

## Read Write Transaction

## Read Only Transaction