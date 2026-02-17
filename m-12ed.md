---
id: m-12ed
status: open
deps: []
created: 2026-02-12T01:22:01Z
type: bug
priority: 2
assignee: Adam
tags: [grammar, error-messages, dx]
updated: 2026-02-12T01:22:11Z
---
# Clarify error for for...when with static condition

Using 'for parallel(N) @item in @list when @staticCond [block]' where the when condition doesn't reference the iteration variable triggers the generic 'when @cond [...] is ambiguous' error. The error is correct but could additionally note: 'If you want to conditionally skip the entire loop, pre-filter the list: var @items = @cond ? @list : []'. The current error explains when vs if semantics but doesn't address the for-when-static-gate pattern.

## Acceptance Criteria

Error message for for...when with non-item-referencing condition includes guidance about pre-filtering the list.

