---
id: m-9f2d
status: closed
deps: []
created: 2026-02-09T19:56:33Z
type: task
priority: 1
assignee: Adam Avenir
updated: 2026-02-09T19:57:32Z
---
# Fix after-guard example accuracy in mcp-guards.md

The after-guard example in mcp-guards.md uses @output.error which may not resolve correctly for string outputs. Also add clarifying note that @mx.taint in after-guard context reflects the output data's taint. The example itself uses @mx.taint (guard context) not @output.mx.taint, which is correct.

