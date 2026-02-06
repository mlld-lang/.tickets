---
id: m-3b4c
status: closed
deps: []
links: []
created: 2026-01-27T05:14:47Z
type: bug
priority: 1
tags: [stale-check-10]
assignee: Adam Avenir
updated: 2026-01-30T12:12:05Z
---
# OOM from recursive deep-copy in normalizeIterableValue (for-expression iteration)

Every for-expression calls `normalizeIterableValue` which recursively deep-copies the entire iteration source — every array element, every nested object, every field. With large datasets this causes heap OOM even though the underlying data is small.

## Root cause

`interpreter/eval/for-utils.ts` lines 13-68, `normalizeIterableValue()`:

- Lines 47-51: Arrays are `.map(item => normalizeIterableValue(item))` — recursive deep copy
- Lines 58-64: Objects are `Object.entries()` + recursive normalization of every value
- Lines 29-32: StructuredValues with array data also get recursively normalized

This runs on **every for-expression evaluation**. The copies are retained in memory because for-expression results store `metadata.sourceExpression` (AST references) and the results array itself.

## Why it OOMs

In `qa.mld`, the run summary section iterates 54 topics, each doing `for @r in @allResults` where `@allResults` has 817 loaded JSON files. Each iteration:

1. `normalizeIterableValue` deep-copies all 817 result objects + nested fields
2. `toIterable` does another `.map()` creating [index, value] tuples
3. `Array.from(iterable)` at `for.ts:563` copies again
4. Nested for-expressions and `@flat` calls repeat the cycle

Total: 54 topics × 817 results × ~10 nested fields = millions of redundant object allocations. 4.6MB of JSON on disk becomes 4GB+ in memory (~870x amplification).

## Repro

```bash
mlld run qa --phase 4 --run 2026-01-27-2
# OOM crash in the unconditional summary section, before Phase 4 even starts
```

The crash is in the run summary loop (`qa.mld` ~line 409) which runs unconditionally regardless of `--phase`.

## Stack trace signature

```
Builtins_ArrayFrom → 8+ anonymous frames → async/promise chain
```

The `Array.from` at `for.ts:563` is the allocation site. The anonymous frames are the recursive `normalizeIterableValue` calls.

## Fix direction

`normalizeIterableValue` should not deep-copy data that's already in a usable form. Options:

1. **Skip normalization for plain data**: If the value is a plain array/object (no Variables, no StructuredValues), return it as-is. Most iteration sources from glob results are already normalized JSON.

2. **Shallow normalization**: Only unwrap the top level (Variable → value, StructuredValue → data) without recursing into children. Children get normalized lazily when accessed in their own for-expressions.

3. **Memoize**: Cache normalization results by identity so re-iterating the same source doesn't re-copy. The run summary iterates `@allResults` 54 times — each should reuse the same normalized copy.

Option 2 (shallow) is probably the right fix. The recursive normalization was added to handle nested Variables/StructuredValues, but in practice for-expressions evaluate one level at a time — the inner for handles its own source normalization.

## Risks of shallow normalization

The change is ~10 lines in one function, but it's on a critical path (every for-expression).

### JS executor sees raw elements through Proxy

When values reach `js { }` blocks, `createVariableProxy` wraps `variable.value` in a Proxy. But the Proxy's get handler returns raw child elements via `Reflect.get(target, prop)` — it does NOT recursively proxy sub-elements. So if an array element is an ArrayVariable (from a nested for-expression), JS code sees a Variable object, not a plain array.

This means `Array.flat()` in `@flat` wouldn't recognize ArrayVariable elements as arrays (`Array.isArray(ArrayVariable)` = false) and wouldn't flatten them. This may be a latent bug that exists today but is masked by the OOM happening first, or by usage patterns that avoid the specific case. Needs investigation.

### Interpreter field access should be fine

The interpreter's `evaluate` and field access mechanisms already handle Variables and StructuredValues natively. Code like `@r.topic` where `@r` is a StructuredValue works through the interpreter path, not through JS. So the interpreter side should be unaffected.

### Provenance tracking

`attachProvenance` on nested elements would stop happening. Need to check if anything downstream depends on provenance being set on child elements vs. only on the top-level iterable.

### Validation approach

Run full test suite (`npm test`) after the change. The fixture tests cover for-expression behavior extensively. Also manually test nested for + `@flat` patterns from qa.mld/polish.mld.

## Workaround

Gate the summary section in `qa.mld` behind `@runPhase1 || @runPhase2` so it doesn't run for phase 3/4 only. Or increase Node heap: `NODE_OPTIONS="--max-old-space-size=8192" mlld run qa --phase 4`.

## Notes

**2026-01-27T15:04:57Z**

## Progress

Two deep-copy paths eliminated, OOM persists:

### Fix 1: Shallow normalizeIterableValue (committed)
normalizeIterableValue no longer recursively deep-copies array elements and object values. It unwraps Variables/StructuredValues at the top level only.

### Fix 2: ensureVariable bypasses VariableImporter (committed)
ensureVariable was routing all plain iteration values through VariableImporter.createVariableFromValue -> unwrapArraySnapshots, which is a full recursive deep-copy (VariableImporter.ts:674-678 builds a new object for every entry). Now creates variables directly via createObjectVariable/createArrayVariable/etc. for plain types.

### OOM still reproduces
Stack trace shows Builtins_ArrayIteratorPrototypeNext with 6+ identical anonymous frames. Crash during FileHandle::CloseReq::Resolve (libuv fs close callback).

### Remaining suspects
1. createChildEnvironment: constructor allocates ~5 manager objects (CacheManager, ErrorUtils, VariableManager, ImportResolver, CommandExecutorFactory) + Maps + Sets per instance. 44,118 child environments is significant.
2. Accumulated results retention: 54 for-expressions each collect 817 results; if bodies return large objects, that is retained in the results arrays.
3. Environment parent chain: child envs hold parent references, potentially preventing GC of the full chain during iteration.

**2026-01-27T16:41:46Z**

## Key finding: OOM is NOT in for-expression code

### Diagnostic evidence
Added memory diagnostics (console.error) to:
- loadGlobPattern (content-loader.ts) — logs on glob start/end
- evaluateForDirective (for.ts) — logs on each iteration
- evaluateForExpression (for.ts) — logs on each iteration

Result: **ZERO diagnostic lines fired** even with 8GB heap. The process consumed 8.19GB and crashed before any glob loading or for-expression evaluation ran.

### Actual crash site: Builtins_ObjectKeys
The stack trace reveals `Builtins_ObjectKeys` (frame 11) followed by 9 identical anonymous JIT frames. This is a recursive traversal calling Object.keys()/Object.entries() at each level.

### Primary suspect: getAllVariables() in VariableManager
File: interpreter/env/VariableManager.ts lines 484-502

getAllVariables() recursively walks the parent scope chain, calling parent.getAllVariables() and iterating with for-of on each Map. Called by:
- guard-post-hook.ts and guard-pre-hook.ts (during guard evaluation)
- interpreter/index.ts (Object.fromEntries(env.getAllVariables()))

With deeply nested scopes from the qa.mld evaluation (imports, exe blocks, for blocks, when blocks), this recursion creates massive intermediate Maps at each level that are never cached.

### Additional recursive Object.keys paths found
1. CollectionEvaluator.evaluatePlainObject() — recursive Object.entries on nested data
2. extractSecurityDescriptor() in structured-value.ts — recursive on nested values
3. resolveNestedValueInternal() in display-materialization.ts — recursive Object.entries
4. ASTEvaluator.normalizeObject() — recursive Object.entries

### Data is tiny — amplification is from evaluation overhead
- 817 results.json files = 636KB total
- 252 self_review.json files = 546KB total
- 54 session files, 130 atom files
- Total on disk: ~4.6MB
- Total JSON on disk: 4.6MB

The 870x amplification from 4.6MB to 4GB is NOT from data duplication. It is from the recursive scope-chain traversal creating enormous intermediate collections.

**2026-01-27T20:16:13Z**

## Root cause found and fixed

### Actual root cause: exponential recursion in module env serialization

File: interpreter/eval/import/VariableImporter.ts

processModuleExports() serializes each exe's capturedModuleEnv via serializeModuleEnv(), which recursively calls processModuleExports() again. Circular reference detection used identity comparison (===) on the Map objects, but captureModuleEnvironment() (Environment.ts:2214) creates a new Map() each time. So each exe's captured env was a different Map instance, causing the identity check to always fail, triggering exponential recursion.

For @mlld/array with 31 exe blocks, this meant O(31^k) recursive serializations until OOM.

### Fix

Added a WeakSet<object> parameter (seen) threaded through serializeModuleEnv and processModuleExports. Before serializing a Map, it's checked against the WeakSet. If already being serialized, it's skipped (treated as circular). This prevents re-entry on any Map, regardless of identity.

### Result

- Peak RSS: 287MB (down from 4GB+ OOM crash)
- All 230 test files pass (3091 tests)
- qa repro runs to completion with full output
