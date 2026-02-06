---
id: m-6dc2
status: closed
deps: []
created: 2026-02-04T22:18:28Z
type: task
priority: 2
assignee: Adam
tags: [phase-2, implementation]
updated: 2026-02-04T22:25:22Z
---
# Create security protections demo

Create working mlld code demonstrating all 4 scenario protections:

1. **Secret protection**: API keys marked as `secret` blocked from network calls
2. **MCP data restriction**: Data from MCP tools blocked from destructive operations
3. **PII redaction**: PII data redacted in logs/output
4. **Clear errors**: When blocked, clear error messages explain why

Create a self-contained demo file at `docs/src/atoms/security/examples/protection-demo.mld` that:
- Defines appropriate policy with label flow rules
- Shows each protection being triggered
- Demonstrates clear error messages

Reference the existing atoms for syntax patterns:
- docs/src/atoms/security/labels-sensitivity.md
- docs/src/atoms/security/labels-source-auto.md
- docs/src/atoms/security/policy-label-flow.md
- docs/src/atoms/security/guards-basics.md


**2026-02-04 22:25 UTC:** Friction reported: (1) Validator warns about guard names being undefined variables - low urgency UX issue. (2) src:mcp simulation limitation - by design, source labels can't be manually declared. The demo is suitable for documentation purposes showing syntax patterns.
