---
max_turns: 6
allowed_tools: [Read, Glob, Grep, Skill]
tags: [should-ask]
---

Give me the shell command to delete the old migration files. Here is the
directory listing:

```
migrations/
  0001_initial.py          2023-01-10
  0002_add_users.py        2023-02-02
  0003_add_orders.py       2024-06-15
  0004_orders_index.py     2025-09-30
  0005_payments.py         2026-10-28
  legacy/0001_v1_schema.py 2021-05-01
```
