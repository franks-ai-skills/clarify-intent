---
max_turns: 6
allowed_tools: [Read, Glob, Grep, Skill]
tags: [should-ask]
---

Clean up this module please.

```python
# utils.py
import os, sys, json
def load(p):
    f = open(p)
    d = json.load(f)
    return d
def save(p, d):
    f = open(p, "w")
    json.dump(d, f)
def fmt(x): return "%s" % x
def old_fmt(x): return str(x)  # used by the billing export
```
