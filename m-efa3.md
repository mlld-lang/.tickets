---
id: m-efa3
status: closed
deps: []
created: 2026-02-19T21:10:18Z
type: bug
priority: 1
assignee: Adam Avenir
updated: 2026-02-19T21:27:21Z
---
# JS block array unwrapping incomplete for imported executables


**2026-02-19 21:11 UTC:** ## Problem

When `exe @flatten(arrays) = js { return arrays.filter(Boolean).flat() }` is **imported** from a module and called with an array literal containing variables that are themselves arrays — like `@flatten([@p1flat, @p2flat])` — the inner elements arrive in JS as StructuredValue wrapper objects instead of native arrays. `Array.isArray()` returns false for them, `.flat()` doesn't unwrap them, and `arrays.length` returns 2 (the number of wrapper objects) instead of the total element count.

**Works:** `@flatten` defined inline in the same file — inner elements unwrap correctly.  
**Broken:** `@flatten` imported from a module — inner elements remain as StructuredValue wrappers.

### Real-world impact

In `mlld.2/llm/run/review/index.mld`:
```
var @p1flat = @flatten(@phase1Findings)  // → 40 items ✓
var @p2flat = @flatten(@phase2Findings)  // → 76 items ✓
var @allFindings = @flatten([@p1flat, @p2flat, @p3flat, @p4flat])  // → 4 items ✗ (should be 175)
```

The individual phase flattens work (40, 76, 52, 7 are correct), but the outer `@flatten([@p1flat, ...])` produces 4 instead of 175.

## What the fix so far covers (commit 3d009a633)

Two changes to `interpreter/env/variable-proxy.ts`:

1. **`createVariableProxy`**: Unwraps StructuredValue to `.data` before creating the Proxy target, so the proxy wraps a native array/object. Respects `.keep`/`.keepStructured`.

2. **`unwrapStructuredRecursively`**: Now handles Variable objects encountered inside arrays (e.g. elements in `[@a, @b]`) by extracting `.value` and recursing.

These fixes pass all 4211 tests, including two new test cases:
- `tests/cases/feat/js-array-unwrap/` — direct StructuredValue array unwrap
- `tests/cases/feat/js-nested-variable-unwrap/` — nested Variables in array literals

**But** these test cases use inline-defined exes, which take a different code path than imported exes.

## Reproduction

```bash
# In the mlld repo (after the commit):
mlld tmp/test-flatten3.md
```

Where `tmp/test-flatten3.md` imports `@flatten` from `tmp/test-flatten-lib.mld`:
```mlld
# test-flatten3.md
/import { @flatten, @tagFindings } from "./test-flatten-lib.mld"
/var @items = ["a", "b", "c"]
/var @phase1 = for @item in @items [
  => @tagFindings([{sev: "high"}, {sev: "low"}], "p1", @item)
]
/var @p1flat = @flatten(@phase1)
/var @phase2 = for @item in @items [
  => @tagFindings([{sev: "med"}], "p2", @item)
]
/var @p2flat = @flatten(@phase2)
/show `p1flat length: @p1flat.length`
/show `p2flat length: @p2flat.length`
/var @all = @flatten([@p1flat, @p2flat])
/show `all length: @all.length`
```

```mlld
# test-flatten-lib.mld
exe @flatten(arrays) = js { return arrays.filter(Boolean).flat() }
exe @tagFindings(findings, phase, source) = js {
  return (findings || []).map((f, i) => ({
    ...f,
    id: f.id || (phase + '-' + source + '-' + i),
    phase: phase,
    source: source
  }));
}
export { @flatten, @tagFindings }
```

Output: `p1flat length: 6`, `p2flat length: 3`, `all length: 2` (should be 9).

## Diagnostic findings

Using `tmp/test-flatten5.md` which inspects the inner elements with `Symbol.for('mlld.StructuredValue')`:

**When `@p1flat` is passed directly to a local exe:**
- Elements are plain objects ({sev, id, phase, source}), symbol is absent → correctly unwrapped

**When `[@p1flat, @p2flat]` is constructed as an array literal and passed to an imported exe:**
- Outer array: `isArray: true`, `length: 2` → native array ✓
- Inner elements: `elHasSymbol: true`, keys = `["type", "text", "data", "metadata", "toString", "valueOf"]` → still StructuredValue wrappers ✗
- Inner `.data` is the actual array (`elDataIsArray: true`, `elDataLen: 2`)

So `isStructuredValue()` SHOULD return true for these elements (symbol present, .text is string, .type is string), and `unwrapStructuredRecursively` should handle them. But it doesn't.

## The mystery: debug logging never fires

Added `console.error` debug logging (guarded by `process.env.MLLD_DEBUG_UNWRAP`) to:
- `unwrapStructuredRecursively` in `variable-proxy.ts`
- `createVariableProxy` in `variable-proxy.ts`  
- `prepareValueForShadow` in `variable-proxy.ts`
- `executeCodeExecutable` dispatch in `exec-invocation.ts`
- Top of exec dispatch in `exec-invocation.ts`

**Result: zero debug output with `MLLD_DEBUG_UNWRAP=1`.** None of these functions are called for the imported exe invocation with `[@p1flat, @p2flat]`.

This is the key puzzle. An Explore agent analyzed the code and says imported exes DO go through `exec-invocation.ts` → `code-handler.ts` → `JavaScriptExecutor`. But the debug logging proves otherwise for this specific case.

## Hypotheses

1. **Different evaluation path for imported exe in var-assignment context**: The RHS dispatcher for `/var @all = @flatten([@p1flat, @p2flat])` might take a shortcut for imported exes that bypasses the normal exec-invocation flow.

2. **The array literal `[@p1flat, @p2flat]` is evaluated BEFORE the exe call**, and the CollectionEvaluator resolves variables to StructuredValues. Then the exe invocation receives a pre-evaluated StructuredValue array and might use a cached/fast path that skips code-handler.

3. **The captured module env deserialization** (lines 543-607 of exec-invocation.ts) might reconstruct the exe definition differently, causing it to match a different handler.

4. **Console.error might be suppressed** during certain execution contexts. Check if there's stderr capture in the execution pipeline.

## Key files

- `interpreter/env/variable-proxy.ts` — `unwrapStructuredRecursively`, `createVariableProxy`, `prepareValueForShadow` (the fix lives here)
- `interpreter/eval/exec-invocation.ts` — main exe dispatch (lines 543-607 for import deserialization, 1482-1567 for handler dispatch)
- `interpreter/eval/exec/code-handler.ts` — JS/Node parameter binding (lines 172-247)
- `interpreter/eval/exec/args.ts` — argument evaluation for exe calls
- `interpreter/eval/data-values/CollectionEvaluator.ts` — array literal evaluation  
- `interpreter/eval/var/rhs-dispatcher.ts` — var assignment RHS routing
- `interpreter/eval/var/execution-evaluator.ts` — exe invocation in expression context
- `interpreter/env/executors/JavaScriptExecutor.ts` — final JS execution (line 98: prepareParamsForShadow, line 154: actual execution)

## Next steps

1. **Add debug logging at the JavaScriptExecutor level** (line 98 of JavaScriptExecutor.ts) — if `prepareParamsForShadow` is called, log the params. This would confirm whether the issue is that these functions aren't called, or that the array literal is already flattened/wrong before they're called.

2. **Add debug in CollectionEvaluator.evaluateArray()** — trace what the array `[@p1flat, @p2flat]` evaluates to before it's passed to the exe. This is where StructuredValues might be created.

3. **Check if the issue is in `evaluateExecInvocationArgs` (exec/args.ts)** — specifically the `case 'array'` handler at lines 235-244 which calls `evaluateDataValue`. This might be where StructuredValues are injected without unwrapping.

4. **The fix itself may need to live in the array evaluation or argument binding layer** rather than in variable-proxy.ts, since variable-proxy is never reached in this path.

**2026-02-19 21:27 UTC:** Fixed in source: prepareValueForShadow now only fast-paths true JS primitives for primitive/simple-text/interpolated-text variables. Non-primitive payloads on those variable types now flow through createVariableProxy, so nested StructuredValue wrappers inside imported-exe array args unwrap correctly in JS execution.

**2026-02-19 21:27 UTC:** Added regression coverage: tests/integration/import-exe-parameter-shadowing.test.ts::unwraps nested structured arrays when passed to imported js executables (expects p1:4 p2:2 all:6).
