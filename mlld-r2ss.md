---
id: mlld-r2ss
status: closed
deps: []
links: []
created: 2026-01-12T14:45:17.318864-08:00
type: bug
priority: 1
---
# Parallel serialization loses nested glob-loaded objects

## Summary

When glob-loaded JSON objects are wrapped inside another object (e.g., `{ topic: @f.topic, fix: @f }`), then accessed inside a parallel loop, the inner object's properties become inaccessible. Errors are silently swallowed.

## Related

This is separate from mlld-nfkj (JSON parsing fix), which has been resolved. This bug only manifests AFTER that fix is applied.

## Reproduction

```mlld
var @proposedFixes = <@base/qa/**/proposed-fix.json>
var @testItems = @proposedFixes.slice(0, 2)

>> This works - direct access
for parallel(2) @item in @testItems [
  show \`@item.topic: @item.recommendation.fix_id\`  >> outputs correctly
]

>> This fails - wrapped access  
exe @wrap(fixes) = for @f in @fixes => { topic: @f.topic, fix: @f }
var @wrapped = @wrap(@testItems)

for parallel(2) @item in @wrapped [
  show \`Outer: @item.topic\`     >> works
  show \`Inner: @item.fix.topic\`  >> silently fails, no output
]
```

## Impact

This is the ACTUAL blocker for polish.mld Phase 3a. The pattern:
```mlld
let @needsApply = for @f in @proposedFixes => { topic: @f.topic, fix: @f }
for parallel(5) @item in @needsApply [
  let @fix = @item.fix  
  // @fix.investigation.root_cause fails silently here
]
```

## Root Cause Hypothesis

Parallel execution serializes objects to pass across boundaries. Glob-loaded objects have special metadata (mx, etc.) that may not serialize/deserialize correctly when nested inside wrapper objects.

## Workaround

Don't wrap glob objects - access them directly:
```mlld
for parallel(5) @f in @proposedFixes when @f.recommendation.auto_approve [
  // Access @f.investigation.root_cause directly - this works
]
```

## Test Files

- tmp/test-wrapped-glob.mld
- tmp/test-inline-vs-wrapped.mld


