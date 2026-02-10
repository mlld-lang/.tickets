---
id: m-6ec8
status: closed
deps: []
created: 2026-02-09T12:06:02Z
type: task
priority: 2
assignee: Adam Avenir
tags: [urgency-high, security-fix, phase-4-remediation]
updated: 2026-02-09T12:27:55Z
---
# Fix taint propagation through when-expressions and for-loops

CRITICAL SECURITY FIX: When-expressions and for-loops strip all taint/labels from return values, creating bypass paths for ALL label-based security rules.

## Evidence
- `var secret @key = 'sk-123'; var @clean = when @key [* => @key]; @exfilOp(@clean)` — secret leaks unblocked
- `var untrusted @bad = 'payload'; var @list = [@bad]; var @clean = for @item in @list [=> @item]; @destructiveOp(@clean)` — untrusted flows unblocked
- Adversarial test files: tmp/adversarial-when-bypass-detail.mld, tmp/adversarial-for-bypass-detail.mld

## Root Cause Analysis

### When-expression (interpreter/eval/when-expression.ts)
- `buildResult()` at line 299 returns `{ value, env }` with NO security descriptor
- `normalizeActionValue()` at line 575 can unwrap StructuredValues, losing `.mx` metadata
- The `actionResult.value` coming from `evaluateActionNodes()` MAY carry descriptors (as StructuredValue or via ExpressionProvenance), but they're stripped before `buildResult` is called
- **Fix pattern**: Before `normalizeActionValue()` at line 575, extract the security descriptor from the raw action result. After normalization, re-attach it to the final value using `ensureStructuredValue()`. Same for the none branch at lines 732-744.
- **Reference**: foreach.ts lines 198-210 shows the exact pattern — extract descriptor, merge with existing, wrap with ensureStructuredValue

### For-expression (interpreter/eval/for.ts)
- At lines 975-993, `asData(branchValue)` strips StructuredValue wrappers including security metadata
- At line 999, `extractVariableValue()` returns raw values without Variable metadata
- At lines 1122-1134, `createArrayVariable()` builds the result with no security descriptor
- **Fix pattern**: Before `asData()` at line 979/989, extract the security descriptor. Collect descriptors from all iterations and merge them. When creating the final ArrayVariable at line 1122, attach the merged descriptor. Also need to handle the case where individual elements carry descriptors from the iteration variable.
- **Reference**: foreach.ts lines 88-99 (extract source descriptors), 173-180 (merge tuple descriptors), 198-210 (apply to results)

## Required Imports

### when-expression.ts already has:
- `ensureStructuredValue` from '../utils/structured-value' (line 18)

### when-expression.ts needs to add:
- `extractSecurityDescriptor` from '../utils/structured-value'
- `SecurityDescriptor` type from '@core/types/security'
- `mergeDescriptors` from '@core/types/security'

### for.ts needs to add:
- `ensureStructuredValue, extractSecurityDescriptor` from '../utils/structured-value' (it already imports asData, asText, isStructuredValue from there)
- `SecurityDescriptor` type and `mergeDescriptors` from '@core/types/security'
- `setExpressionProvenance` from '@core/types/provenance/ExpressionProvenance'

## Verification
After fixing, these adversarial tests should show labels preserved:
1. `mlld tmp/adversarial-when-bypass-detail.mld` — secret labels should persist through when, exfil should be BLOCKED
2. `mlld tmp/adversarial-for-bypass-detail.mld` — untrusted labels should persist through for, destructive should be BLOCKED
3. `mlld tmp/adversarial-sensitive-bypass.mld` — sensitive labels should persist through when/for
4. All existing tests must still pass: `npm test`

## Scope
Do NOT fix the + operator NaN/taint issue — that's a separate language design choice (+ is numeric, `@a@b` is string concat). The data is corrupted to NaN so it's not a real exfiltration path.

