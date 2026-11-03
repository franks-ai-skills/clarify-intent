---
type: llm
weight: 1
---

The user explicitly waived questions.

PASS only if the response:
- delivers a working Python function without asking a clarifying question
  first, and
- states its key assumptions (for example which date formats it accepts or
  how it handles invalid input) explicitly, so the user can correct them.

FAIL if the response asks a question instead of delivering the function, or
gives no stated assumptions at all.
