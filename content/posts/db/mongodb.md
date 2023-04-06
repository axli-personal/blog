---
title: "Mongodb"
date: 2023-01-31T09:16:24+08:00
tags: []
categories: ["Database"]
summary: "How To Update Array, How To Shard Collection."
---

## How To Update Array

The `$` operator acts as a placeholder for the first match of the update query document.

```js
{
  _id: 4,
  grades: [
     { grade: 80, mean: 75, std: 8 },
     { grade: 85, mean: 90, std: 5 }, // 6
     { grade: 85, mean: 85, std: 8 }
  ]
}

db.students.updateOne(
   { _id: 4, "grades.grade": 85 },
   { $set: { "grades.$.std" : 6 } }
)
```

The `$pop` operator removes the first or last element of an array.

```js
{
  _id: 1,
  scores: [ 8, 9, 10 ] // [ 9, 10 ]
}

db.students.updateOne( { _id: 1 }, { $pop: { scores: -1 } } )
```

The `$pull` operator removes from an array all instances of values that match a specified condition.

```js
{
  _id: 1,
  votes: [ 3, 5, 6, 7, 7, 8 ] // [ 3,  5 ]
}

db.profiles.updateOne( { _id: 1 }, { $pull: { votes: { $gte: 6 } } } )
```

## How To Shard Collection

```js
// namespace: <database>.<collection>
// key: { <field1>: <1|"hashed"> }
// 1: ranged based sharding
// hashed: hashed based sharding
sh.shardCollection(namespace, key)
```
