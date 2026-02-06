---
id: m-d49e
status: closed
deps: []
created: 2026-02-04T04:44:19Z
type: task
priority: 2
assignee: Adam
tags: [urgency-high, j2bd-mcp, security-bug]
updated: 2026-02-04T04:54:50Z
---
# Fix policy label flow check missing for template executables

## Bug

Policy label flow rules (`policy.labels.src:mcp.deny`) are not enforced for template executables (`exe destructive @destroy(data) = \`destroyed: @data\``). The `checkLabelFlow` call is missing for template-type exes.

## Root Cause

In `interpreter/eval/exec-invocation.ts`, the policy label flow check block (lines 2197-2293) only covers three executable types:
- `isCommandExecutable` (line 2197) - has checkLabelFlow at line 2228
- `isCodeExecutable` (line 2255) - has checkLabelFlow at line 2266
- `isNodeFunctionExecutable` (line 2276) - has checkLabelFlow at line 2283

Template executables (`isTemplateExecutable`, line 2491) are NOT in this block and skip policy checking entirely. Same for `isCommandRefExecutable` (line 3333), `isSectionExecutable` (line 3470), and `isResolverExecutable` (line 3525).

## Fix

Add an `else if (isTemplateExecutable(definition))` branch after the `isNodeFunctionExecutable` block (after line 2293) that calls `checkLabelFlow` with the correct parameters. Follow the pattern from the `isCodeExecutable` branch (simplest):

```typescript
} else if (isTemplateExecutable(definition)) {
    const inputTaint = descriptorToInputTaint(mergePolicyInputDescriptor(resultSecurityDescriptor));
    if (inputTaint.length > 0) {
      policyEnforcer.checkLabelFlow(
        {
          inputTaint,
          opLabels: [],
          exeLabels,
          flowChannel: 'arg'
        },
        { env, sourceLocation: node.location }
      );
    }
}
```

Template exes don't have opLabels (no cmd/sh/node type), but they DO have exeLabels from the exe definition (e.g., `destructive`). The exeLabels are already correctly extracted at line 2184.

Also consider adding the same pattern for `isCommandRefExecutable`, `isSectionExecutable`, and `isResolverExecutable` to be categorical.

## Verification

```mlld
import tools { @echo } from mcp "node tests/support/mcp/fake-server.cjs"
policy @p = {
  labels: {
    "src:mcp": {
      deny: ["destructive"]
    }
  }
}
var @mcpData = @echo({ text: "mcp data" })
exe destructive @destroy(data) = `destroyed: @data`
var @result = @destroy(@mcpData)
show @result
```

Expected: Policy denial error. Currently outputs: `destroyed: mcp data`


**2026-02-04 04:54 UTC:** Fixed: Added checkLabelFlow after parameter descriptor collection in exec-invocation.ts. The early check block (lines 2197-2306) runs before parameter security descriptors are populated, so inputTaint is always empty for exe arguments. The fix adds a checkLabelFlow call after resultSecurityDescriptor is built from bound parameter descriptors (line ~2405), catching all executable types including template, commandRef, section, and resolver. Also added catch-all else in early block for completeness. Test added to mcp-import.test.ts. Commit: c48263a81
