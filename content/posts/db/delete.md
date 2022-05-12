---
title: "Delete"
date: 2022-05-04T13:00:00+08:00
summary: "Basic SQL Statement"
---

## Multi-Table Delete

```mysql
DELETE FROM
    person
USING
    person, person AS self # Table Reference
WHERE
    person.email = self.email AND person.id > self.id;
```
