---
id: m-dec8
status: closed
deps: []
created: 2026-02-04T05:10:09Z
type: bug
priority: 1
assignee: worker
tags: [urgency-high, j2bd-mcp, security-bug]
updated: 2026-02-04T05:18:04Z
---
# Fix object literal label propagation - evaluateArrayItem VariableReference case

## Acceptance Criteria

1. var secret @key; var @obj = { text: @key }; show @obj.mx.labels outputs secret label
2. Same for arrays: var @arr = [@key]; show @arr.mx.labels outputs secret label  
3. All existing tests pass (npm test)
4. Guard with @input.any.mx.labels.includes secret blocks @echo with object arg


**2026-02-04 05:18 UTC:** Fixed in 5f04b9ea0. Three changes: (1) evaluateArrayItem VariableReference case now calls collectDescriptor with the variable's security metadata before returning. (2) Complex/lazy objects and arrays pre-scan AST for VariableReference nodes to extract security descriptors before creating the variable. (3) build-fixtures.mjs now falls back to .md extension when .mld replacement doesn't match. All 46 security tests pass including collection-label-propagation-object and collection-label-propagation-array.
