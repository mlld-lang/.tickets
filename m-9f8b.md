---
id: m-9f8b
status: closed
deps: [m-335e]
created: 2026-02-18T01:27:36Z
type: task
priority: 0
assignee: Adam Avenir
updated: 2026-02-18T06:50:16Z
tags: [docs, guards]
---
# Rewrite before-guards.md: fix wrong trigger descriptions

before-guards.md reinforces the wrong mental model from guards-basics.md.

**Problems:**
- Lines 14-17: Same wrong table as guards-basics (data guards fire 'when labeled data is created')
- Line 37: 'Data validation guards — validate or sanitize at label-entry time' — wrong framing
- Line 50: 'These fire when data receives a label, before any operation context exists' — completely wrong. They fire in full operation context with @mx.op available.

**Fix:**
- Replace table to match new guards-basics framing
- Reframe 'data validation guards' section as 'per-input guards' — fire for each input carrying the matching label
- Delete line 50 entirely — it's the single most misleading sentence in the guard docs
- Show that @mx.op.type, @mx.op.labels etc. are available in per-input guards
- Update examples: transform-with-allow.md already has guard @redact before secret checking @mx.op.type == 'show' — reference this as the correct pattern

**Acceptance criteria:**
- No claim that data guards fire 'at creation time' or 'before operation context exists'
- Clear that per-input guards run in full operation context
- Consistent with new guards-basics.md framing

