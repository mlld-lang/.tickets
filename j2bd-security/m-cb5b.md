---
id: m-cb5b
status: closed
deps: []
created: 2026-02-05T13:32:26Z
type: task
priority: 2
assignee: Adam
tags: [phase-2, demo]
updated: 2026-02-05T13:34:13Z
---
# Create comprehensive security demo

Create a demonstration script showing all four protection scenarios from the job specification:

1. **Secret data blocked from network calls** - Show that `var secret @apiKey` cannot be interpolated into curl/fetch commands via policy label flow rules

2. **MCP-sourced data blocked from destructive operations** - Show that data with `src:mcp` taint cannot flow to operations labeled `destructive`

3. **PII redaction in logs/output** - Show that `var pii @email` gets redacted when shown/logged using guards

4. **Clear error messages** - Verify that blocked operations show messages like the ones in tests/cases/security/effect-guard-show/error.md

**Implementation guidance:**
- Create `tmp/security-protection-demo.mld` with all four scenarios
- Use `mlld validate` to verify syntax
- Each scenario should demonstrate both the blocking AND the clear error message
- Can reference existing test cases for patterns:
  - tests/cases/security/effect-guard-show/ - secret blocking pattern
  - tests/cases/security/policy-composition-layers/ - label flow rules pattern
  - tests/cases/docs/security/22/ - redaction pattern

**Existing atoms to reference:**
- docs/src/atoms/security/labels-sensitivity.md
- docs/src/atoms/security/labels-source-auto.md  
- docs/src/atoms/security/policy-label-flow.md
- docs/src/atoms/security/guards-basics.md


**2026-02-05 13:34 UTC:** All 4 protection demos verified working. Proceeding to adversarial verification.
