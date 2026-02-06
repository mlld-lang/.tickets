---
id: m-5c5d
status: closed
deps: []
created: 2026-02-04T11:05:55Z
type: task
priority: 2
assignee: Adam
tags: [urgency-high, security-bypass, phase-3-4]
updated: 2026-02-04T11:25:02Z
---
# Fix commandRef exe wrapper security label loss

## Critical Security Bypass

When an exe calls another exe via commandRef (e.g., `exe @wrapper(data) = @innerPost(@data)`), security labels on arguments are lost. This allows secret data to leak through unlabeled wrapper functions.

## Root Cause

In `interpreter/eval/exec-invocation.ts`, the commandRef execution path (lines 3395-3530) evaluates args to raw values that lose their Variable security context:

1. **Line 3457-3477 (with commandArgs):** Args are evaluated via `evaluate()` or `interpolateWithResultDescriptor()`, producing raw values. These are pushed to `refArgs` array.
2. **Line 3497:** `args: refArgs` passes raw values (not Variables with `mx.labels`) to the recursive `evaluateExecInvocation`.
3. **Line 3514-3527 (without commandArgs):** `args: evaluatedArgs` passes the pre-evaluated args, which are also raw values.

When the inner exe processes these args, `collectAndMergeParameterDescriptors` (line 2430) finds no security descriptors on the newly-created parameter variables, so the label flow check at line 2444 sees empty `inputTaint` and passes.

## Fix Strategy

Preserve security descriptors through the commandRef arg passing. Two approaches:

### Approach A: Attach security descriptors to arg values
For each `refArg`, extract the security descriptor from the source variable and attach it to the value (if it's a StructuredValue, set its metadata.security). For string values, wrap them in StructuredValues with the security descriptor.

### Approach B: Pass security context alongside args
Extract the merged security descriptor from `resultSecurityDescriptor` (which already captures param descriptors at line 2438) and propagate it to the refEnv so the inner evaluateExecInvocation can access it.

### Recommended: Approach A
The cleanest fix is to ensure `refArgs` carry their security descriptors. After evaluating each arg:
1. Check if the arg came from a parameter variable in `execEnv` (line 3469 already does this check)
2. If so, also extract its security descriptor from the parameter variable's `mx`
3. Wrap the value in a StructuredValue with the security descriptor if it isn't already structured
4. Same treatment for the no-commandArgs path at line 3519 - `evaluatedArgs` should carry descriptors

Alternatively, look at how the direct exe call path preserves labels at lines 2396-2421 (the `allowOriginalReuse` path in createParameterVariable) and replicate that pattern for the commandRef recursive call.

## Test Cases

- `tmp/adversarial-exe-inner-labels.mld` - single wrapper, should block
- `tmp/adversarial-double-wrap.mld` - double wrapper, should block
- Direct call (no wrapper) must continue to work

## Files to Modify

- `interpreter/eval/exec-invocation.ts` - commandRef execution path (lines ~3395-3530)

