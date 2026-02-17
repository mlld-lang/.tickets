---
id: m-7f5d
status: closed
deps: []
created: 2026-02-16T00:51:54Z
type: feature
priority: 0
assignee: Adam
updated: 2026-02-16T01:59:53Z
---
# Implement tool-call-tracking per spec

## Problem

`@mx.tools.calls`, `@mx.tools.allowed`, and `@mx.tools.denied` always return empty arrays when accessed from guards. The infrastructure exists but is only wired up in the MCP path, not in the normal exe invocation path.

## What to do

Call `env.recordToolCall()` in `interpreter/eval/exec-invocation.ts` (around line 145, `evaluateExecInvocation`) whenever an exe function is invoked. Record the function name and arguments.

The MCP path already does this correctly — see `cli/mcp/FunctionRouter.ts` lines 92 and 114 for the pattern. Follow that same `ToolCallRecord` structure.

Also call `env.setToolsAvailability()` when MCP tool sets are configured, using the pattern from `cli/mcp/MCPServer.ts` lines 235-247.

## Key files

- `interpreter/eval/exec-invocation.ts` — where exe functions are invoked (line 145). Add recordToolCall here.
- `interpreter/env/ContextManager.ts` — defines recordToolCall (line 266), setToolAvailability (line 261), getToolsSnapshot (line 274)
- `interpreter/env/Environment.ts` — wrapper methods at lines 1472-1477
- `cli/mcp/FunctionRouter.ts` — reference implementation that already calls recordToolCall (lines 92, 114)
- `todo/spec-security-2026-v5.md` — spec sections 1.9 and 14.9 describe expected behavior

## Acceptance criteria

- `@mx.tools.calls` contains entries after exe functions are invoked
- Guards can check `@mx.tools.calls.includes("functionName")` and get true
- Existing MCP tool-call tracking continues to work
- Add test cases in `tests/cases/` for tool-call-tracking


**2026-02-16 01:59 UTC:** Implemented tool-call tracking for direct executable invocation in interpreter/eval/exec-invocation.ts (including guard-visible @mx.tools.calls), avoided MCP double-recording via router marker metadata, and populated @mx.tools.allowed/@mx.tools.denied from env mcpConfig registration. Added regression fixture tests/cases/security/tool-call-tracking-exe-guards and integration coverage in interpreter/eval/env-mcp-config.test.ts. Full test suite passes (4205 passed, 58 skipped).
