---
id: m-e27a
status: closed
deps: []
created: 2026-02-04T05:57:35Z
type: task
priority: 2
assignee: Adam
tags: [urgency-high, j2bd-mcp]
updated: 2026-02-04T06:03:36Z
---
# Fix inline object/array label propagation at exe call sites

## Problem

Inline object literal arguments at exe/MCP call sites don't propagate security labels from constituent variables. This is EC3's remaining failure.

**Works:** `var @obj = { text: @apiKey }; @echo(@obj)` - fix 5f04b9ea0 handles var assignment
**Broken:** `@echo({ text: @apiKey })` - inline object at call site loses secret label

## Root Cause

In `interpreter/eval/exec-invocation.ts`:

1. **Lines 1854-1866**: `object` and `array` args are evaluated via `evaluateDataValue()` which returns a plain JS object. Security descriptors collected during evaluation are recorded in the environment but NOT attached to the returned value.

2. **Lines 2005-2054**: The `guardVariableCandidates` array only populates for `VariableReference` args (line 2007). For `object`/`array` args, the entry stays `undefined`.

3. **Line 2103-2105**: `materializeGuardInputsWithMapping` falls back to the raw evaluated arg, which has no security metadata.

## Fix

Follow the same pattern as `var.ts` (commit 5f04b9ea0):

### Step 1: Import `extractDescriptorsFromDataAst` from var.ts

This function is already exported and does exactly what we need - walks an AST node to find VariableReference nodes and collects their security descriptors.

### Step 2: In the arg-type loop (lines 2005-2054), add cases for `object` and `array` args

After the existing `VariableReference` case (line 2007) and `ExecInvocation` case (line 2035), add:

```typescript
} else if (
  arg && typeof arg === 'object' && 'type' in arg &&
  (arg.type === 'object' || arg.type === 'array')
) {
  // Pre-scan inline object/array AST for variable references with security labels
  const dataDescriptor = extractDescriptorsFromDataAst(arg, env);
  if (dataDescriptor && (dataDescriptor.labels.length > 0 || dataDescriptor.taint.length > 0)) {
    // Create a Variable wrapper with the merged descriptor as guard candidate
    const syntheticVar = createSimpleTextVariable(
      '__inline_arg__',
      evaluatedArgStrings[i] ?? '',
      { directive: 'var', syntax: 'expression', hasInterpolation: false, isMultiLine: false }
    );
    syntheticVar.value = evaluatedArgs[i];
    if (!syntheticVar.mx) syntheticVar.mx = {};
    updateVarMxFromDescriptor(syntheticVar.mx, dataDescriptor);
    if ((syntheticVar.mx as any).mxCache) delete (syntheticVar.mx as any).mxCache;
    guardVariableCandidates[i] = syntheticVar;
  }
}
```

### Step 3: Merge descriptors into resultSecurityDescriptor

Also merge the extracted descriptors into `resultSecurityDescriptor` so policy label flow checks see them. Add this right after the guard candidate loop, or integrate it where `mergeResultDescriptor` is called.

Specifically, after the guard candidate creation loop ends (~line 2086), iterate `guardVariableCandidates` and merge any new descriptors into `resultSecurityDescriptor`:

```typescript
for (const candidate of guardVariableCandidates) {
  if (candidate?.mx) {
    const desc = varMxToSecurityDescriptor(candidate.mx);
    if (desc) mergeResultDescriptor(desc);
  }
}
```

Wait - `mergeResultDescriptor` is in a different scope (the try block starting at line 697). The approach should be: during argument evaluation (lines 1854-1866), also call `mergeResultDescriptor` when the inline object has labels. This is already the right scope.

### Step 4: In the arg evaluation switch (lines 1854-1866), add descriptor collection

```typescript
case 'object':
  const { evaluateDataValue } = await import('./data-value-evaluator');
  argValueAny = await evaluateDataValue(arg, env);
  argValue = JSON.stringify(argValueAny);
  // Collect security descriptors from inline object's variable references
  {
    const dataDescriptor = extractDescriptorsFromDataAst(arg, env);
    if (dataDescriptor) mergeResultDescriptor(dataDescriptor);
  }
  break;
```

Same for `array` case.

### Required imports

`extractDescriptorsFromDataAst` is exported from `interpreter/eval/var.ts`. Import it at the usage site:
```typescript
const { extractDescriptorsFromDataAst } = await import('./var');
```

Or add a static import if the function is moved to a shared utility. For now, dynamic import follows the existing pattern in exec-invocation.ts.

### Verification

After fix, run:
```bash
echo 'import tools { @echo } from mcp "node tests/support/mcp/fake-server.cjs"
guard @noSecretToMcp for secret = when [
  @mx.op.type == "exe" => deny "Secret blocked"
  * => allow
]
var secret @apiKey = "sk-12345-super-secret"
exe @tryEcho(v) = when [ denied => `BLOCKED` ; * => @echo({text: @v}) ]
var @r1 = @tryEcho(@apiKey)
show @r1
var @r2 = @echo({ text: @apiKey })
show @r2' > tmp/test-inline-fix.mld
mlld tmp/test-inline-fix.mld
```

Both @r1 and @r2 should be BLOCKED. Previously only @r1 was blocked.

Also run: `npm test` to verify no regressions.

