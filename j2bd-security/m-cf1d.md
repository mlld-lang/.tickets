---
id: m-cf1d
status: closed
deps: []
created: 2026-02-05T01:49:47Z
type: task
priority: 2
assignee: Adam
tags: [polish, documentation, urgency-high]
updated: 2026-02-05T01:54:02Z
---
# Fix documentation polish gaps from excellence assessment

Excellence assessment identified 3 documentation gaps causing user confusion:

## Gap 1: Misleading 'tools' description in env-config.md
**File**: docs/src/atoms/security/env-config.md
**Line**: 37 (table row)
**Current**: `| \`tools\` | \`["Read", "Write"]\` | Tool restrictions |`
**Fix**: Change 'Tool restrictions' to 'MCP tool routing (does not block commands)'

## Gap 2: Missing clarification note
**File**: docs/src/atoms/security/env-config.md
**Location**: After the configuration table
**Add**: Note explaining: 'The `tools` field routes MCP tool calls, not command execution. To block commands, use `policy.capabilities.deny`.'

## Gap 3: sandbox-demo.mld inline comment
**File**: docs/src/atoms/security/examples/sandbox-demo.mld
**Line**: ~20 (tools config line)
**Add**: Inline comment: `// MCP tool routing (see policy for command blocking)`

## Evidence
- New user test took 15 minutes instead of ~5 due to this confusion
- User set tools: ['Read'], expected Bash blocked, but command ran successfully
- User had to discover the tools vs capabilities distinction through trial and error

## Acceptance
- All 3 fixes applied
- Excellence assessment re-run confirms polish is complete


**2026-02-05 01:54 UTC:** All polish gaps from excellence assessment addressed. Tests pass.
