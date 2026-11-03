---
type: llm
weight: 1
---

"Clean up" can mean very different things here (style only, removing apparently
unused code such as `old_fmt`, which a comment says the billing export uses,
fixing the unclosed file handles, which changes behavior, or a larger
restructure).

PASS only if the response:
- asks the user at least one specific question about what "clean up" should
  cover, ideally with concrete options, and
- does NOT present a finished rewrite that removes `old_fmt` or changes
  behavior as if the scope were already settled.

A short restatement of the goal and a list of proposed defaults are fine.
FAIL if the response just delivers a cleaned-up version without asking, or asks
only a vague question like "any preferences?".
