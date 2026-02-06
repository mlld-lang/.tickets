---
id: m-7cd0
status: closed
deps: []
created: 2026-01-29T18:06:32Z
type: bug
priority: 1
assignee: Adam
tags: [interpreter, structured-value, parallel-for]
updated: 2026-01-29T18:07:07Z
---
# Parallel for results have corrupted field access on nested objects

**FIXED** - Root cause was function parameter shadowing (see m-6959).

## Resolution

The bug was caused by `@logItemDone(runDir, id, result, extra)` having a parameter named `result` that shadowed the caller's `@result` variable. When the function returned, the string `"enriched"` (passed as third argument) leaked back and overwrote the caller's structured object.

**Workaround applied:** Renamed parameter from `result` to `status` in `llm/run/polish/lib/events.mld`

**Root cause fixed:** Updated interpreter to properly scope function parameters (see m-6959)

---

## Original Description

When parallel for collects results from functions that return objects built from StructuredValue parameters, nested object properties become inaccessible with error 'Cannot access field X on non-object value (string)'.

## Acceptance Criteria

- Field access on nested properties of parallel for results works correctly
- Objects returned from functions preserve their nested structure through parallel collection

## Reproduction

In the polish pipeline's enrich phase:

```mlld
exe @_finishEnrichTicket(runDir, issueId, result) = [
  show `    _finish DEBUG: result.staleness.score = @result.staleness.score`  // WORKS

  let @returnObj = {
    ticket_id: @issueId,
    staleness: @result.staleness,  // This property evaluation FAILS silently
    // ...
  }

  show `    _finish DEBUG: returnObj.staleness.score = @returnObj.staleness.score`  // WORKS
  => @returnObj
]

// Later, after parallel for collects results:
let @first = @results[0]
show `  DEBUG: first.ticket_id = @first.ticket_id`        // Returns "m-24a0"
show `  DEBUG: first.staleness = @first.staleness`        // Returns {"__error":true,"__message":"Cannot access field \"staleness\" on non-object value (string)"}
```

## Symptoms

1. Direct field access via `show` works: `@result.staleness.score` returns correct value
2. Object literal construction silently fails: `staleness: @result.staleness` gets wrapped as `{__error: true, ...}`
3. The `__error` object is created by `CollectionEvaluator.createPropertyError()` in `interpreter/eval/data-values/CollectionEvaluator.ts:367`
4. Error message indicates the base value is a STRING when it should be an object

## Investigation Notes

### Code Path Difference

**Template interpolation (works):**
- interpolation.ts → resolveVariable with FieldAccess context → accessField

**Object literal evaluation (fails):**
- CollectionEvaluator.evaluateObject → evaluateDataValue → VariableReferenceEvaluator → accessField

Both paths should behave identically, but something differs.

### Current Theory

The issue may involve StructuredValue handling. When a JSON file is loaded via `<@markerFile>?`:
1. Content loader creates StructuredValue with `.text` = raw JSON string, `.data` = parsed object
2. This is passed as function parameter
3. Parameter becomes a StructuredValueVariable
4. When CollectionEvaluator evaluates `@result.staleness`, something goes wrong

**Potential bug location:** `interpreter/eval/for.ts` lines 668-674:
```typescript
if (isStructuredValue(branchValue)) {
  try {
    branchValue = asData(branchValue);
  } catch {
    branchValue = asText(branchValue);  // Falls back to STRING if asData throws
  }
}
```

If `asData()` throws for any reason, the value becomes a string via `asText()`. This corrupted string value then propagates through the system.

### Files to Investigate

- `interpreter/eval/for.ts` - parallel for result collection
- `interpreter/eval/data-values/CollectionEvaluator.ts` - object literal evaluation
- `interpreter/eval/data-values/VariableReferenceEvaluator.ts` - field access in data context
- `interpreter/utils/field-access.ts` - core field access logic
- `interpreter/utils/structured-value.ts` - StructuredValue implementation

### Related Context

See `docs/dev/DATA.md` for background on StructuredValue implementation. There have been gaps where "everything was a string" before StructuredValue was implemented.

### Investigation Session 2026-01-29

Multiple test cases created to reproduce the bug - ALL PASSED:
- `tmp/test-m-7cd0.mld` - Basic test with JSON loading
- `tmp/test-m-7cd0-v2.mld` - Nested `when first` call chain matching enrich.mld
- `tmp/test-m-7cd0-v4.mld` - Multiple tickets in parallel

Debug logging added to:
- `interpreter/eval/data-values/VariableReferenceEvaluator.ts` - Logs variable type during field access
- `interpreter/utils/field-access.ts` - Logs when "non-object value" error occurs

Key finding: The debug logging shows `variableType: 'structured'` and correct values during test execution. The bug does NOT reproduce in isolated tests.

**Possible explanations:**
1. Race condition in parallel execution that only triggers under specific timing
2. Environment state accumulated from earlier pipeline phases
3. Specific data pattern in certain enrich JSON files
4. Import-related side effects from the multiple module dependencies

**Next steps:**
1. Run actual polish pipeline with `MLLD_DEBUG_FIELD_ACCESS=true` to capture debug output when bug occurs
2. Add more granular logging to trace exact moment of corruption
3. Consider adding assertions/invariant checks in the code path

