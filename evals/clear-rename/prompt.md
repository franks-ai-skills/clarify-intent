---
max_turns: 6
allowed_tools: [Read, Glob, Grep, Skill]
tags: [should-not-ask]
---

In the snippet below, rename the function `calc_ttl` to `calculate_total`,
including its call site. Reply with the updated snippet only.

```python
def calc_ttl(items):
    return sum(i.price for i in items)

print(calc_ttl(cart))
```
