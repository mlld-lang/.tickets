---
id: m-b7ad
status: closed
deps: []
links: []
created: 2026-01-24T23:08:03Z
type: bug
priority: 1
assignee: Adam Avenir
parent: m-2af3
---
# Bind values preserve Variable wrappers instead of raw values

## Problem

When tool collection bind values reference @variables, the Variable wrapper object is preserved instead of extracting the raw value. This causes serialization failures when the bind value is used.

**Example:**
```mlld
var @myOrg = "test-org"
exe @createIssue(owner, title) = js { return owner + "/" + title; }
var tools @x = { create: { mlld: @createIssue, bind: { owner: @myOrg } } }
```

**Expected:** `bind.owner = "test-org"`
**Actual:** `bind.owner = { name: 'myOrg', value: 'test-org', type: 'data', ... }`

## Root Cause

In var.ts, when evaluating bind objects:
1. `@myOrg` reference resolved using `ResolutionContext.ArrayElement`
2. `ArrayElement` context preserves Variable wrappers by design
3. FunctionRouter.resolveToolArgs merges the unextracted Variable object

## Recommended Fix

Extract raw values in FunctionRouter.resolveToolArgs before merging:
```typescript
const extractedBound: Record<string, unknown> = {};
for (const [key, value] of Object.entries(bound)) {
  if (isVariable(value)) {
    extractedBound[key] = extractVariableValue(value);
  } else {
    extractedBound[key] = value;
  }
}
return { ...extractedBound, ...args };
```

## Files
- cli/mcp/FunctionRouter.ts:175-214 (resolveToolArgs)
- interpreter/eval/var.ts:1797-1810 (evaluateArrayItem VariableReference case)

## Tests Needed
- bind with @var reference
- bind with nested object containing @var
- bind with computed value

