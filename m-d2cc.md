---
id: m-d2cc
status: closed
deps: [m-335e]
created: 2026-02-18T01:27:27Z
type: task
priority: 0
assignee: Adam Avenir
updated: 2026-02-18T06:49:19Z
tags: [docs, guards]
---
# Rewrite guards-basics.md: remove false two-trigger-type framing

guards-basics.md is the primary guard reference and the source of the false 'two trigger types' framing that propagates through all other guard docs.

**Problems:**
- Line 32: 'TRIGGER: op:TYPE for operations, LABEL for data validation' — bare labels now match both
- Lines 34-39: 'Two trigger types' table claims data guards fire 'Labeled data created / Once per labeled value' — wrong, they fire before every operation where labeled data is input
- Line 41: 'Label-entry guards validate or sanitize data at creation time' — wrong framing
- Lines 54-58: Context reference table claims before LABEL lacks @mx.op.* — wrong, @mx.op IS available
- Lines 60-66: Aggregate helpers listed only for operation guards — also available via operation matching

**New framing:**
One model: a guard trigger is a label. It matches wherever that label appears — on input data, on the operation, or both. op: narrows to operation-only matching.

Scope distinction:
| Match source | Scope | @input | When |
|---|---|---|---|
| Data label on input | per-input | Individual labeled variable | Each matching input |
| Operation label | per-operation | Array of all inputs | Once per operation |

**Acceptance criteria:**
- No mention of 'label-entry guards' or 'data validation guards' as a separate category
- Table shows one unified model with scope distinction
- Context reference shows @mx.op.* available for all guard types
- Examples show both bare-label and op:-prefixed guards

