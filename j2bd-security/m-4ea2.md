---
id: m-4ea2
status: closed
deps: []
created: 2026-02-04T06:14:18Z
type: task
priority: 2
assignee: Adam
tags: [urgency-high, j2bd-mcp]
updated: 2026-02-04T06:19:44Z
---
# Fix template variable security label loss during exe parameter binding

## Problem

Template (derived) variables lose security labels when passed as exe parameters. This allows secrets to leak through exe wrappers to MCP tools.

**Attack vector**: `var secret @apiKey = "sk-12345"; var @message = \`key: @apiKey\`; @sendViaSlack(@message)` — @message correctly has `mx.labels: ["secret"]` but the guard never fires.

## Root Cause

In `interpreter/eval/exec-invocation.ts` lines 2023-2025:

```typescript
const { isTemplate } = await import('@core/types/variable');
if (isTemplate(variable) && typeof evaluatedArgs[i] === 'string') {
  originalVariables[i] = undefined;
}
```

This intentionally excludes template variables from `originalVariables` (the GOTCHA comment explains: use the interpolated string value, not the template). But it also leaves `guardVariableCandidates[i]` as `undefined`, which means the security metadata (labels, taint, sources) is never passed to the guard system or the parameter binding.

## Fix

After the template exclusion, still populate `guardVariableCandidates[i]` with the variable:

```typescript
if (isTemplate(variable) && typeof evaluatedArgs[i] === 'string') {
  originalVariables[i] = undefined;
  // Still preserve security metadata for guard/policy checks
  guardVariableCandidates[i] = variable;
}
```

This ensures:
1. The template GOTCHA is preserved (originalVariables stays undefined, so createParameterVariable uses the interpolated string)
2. The guard system sees the security labels from the template variable
3. cloneGuardCandidateForParameter (line 2395-2403) creates the parameter with the security metadata intact
4. Policy label flow checks at line 2429 see the security descriptors

## Test

Verify with: `mlld tmp/adversarial-ec3-realistic-bypass.mld`
Expected: Guard blocks the secret from leaking through the exe wrapper

Also verify the demo still works: `mlld pipeline/mcp-security-demo.mld`
Step 3 should still show BLOCKED.

Run `npm test` to ensure no regressions.


**2026-02-04 06:19 UTC:** Implementation complete. Next: re-run adversarial verification on m-9f61 to confirm all 5 exit criteria PASS.
