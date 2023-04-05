---
title: "Redis"
date: 2022-08-12T15:00:00+08:00
categories: ["Database"]
summary: "Redis Usage"
---

## Commands

Redis command doesn't have trailing semicolon.

```bash
SELECT 0  # switch to database 0
KEYS *    # list all keys
MULTI     # Start transaction
EXEC      # Execute all commands
```

## Rollback

Redis **doesn't support** rollbacks of transactions for simplicity and performance.
