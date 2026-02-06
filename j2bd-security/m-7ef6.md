---
id: m-7ef6
status: closed
deps: []
created: 2026-02-05T13:34:59Z
type: task
priority: 2
assignee: Adam
tags: [adversarial, urgency-high]
updated: 2026-02-05T13:54:19Z
---
# Adversarial verification: prove all 4 security protections

## Purpose
Prove that all 4 exit criteria from the security scenario actually work, not just that demos exist.

## Exit Criteria to Verify

1. **Secret blocking**: API keys marked `var secret` cannot leak to network calls (curl, fetch, etc.)
2. **MCP blocking**: Data from MCP tools cannot be used in destructive operations
3. **PII redaction**: PII cannot be logged/displayed without redaction
4. **Clear errors**: Blocked attempts produce clear error messages explaining why

## Test Approach

For each protection:
1. Run the demo file and capture output
2. Verify the claimed behavior actually occurs
3. Try to BREAK the protection - find edge cases that bypass it
4. Document any gaps found

## Demo Files
- docs/src/atoms/security/examples/protection-1-secret.mld
- docs/src/atoms/security/examples/protection-2-mcp.mld  
- docs/src/atoms/security/examples/protection-3-pii.mld
- docs/src/atoms/security/examples/protection-4-errors.mld

## Return Format

Return structured result with:
```
status: closed
exit_criteria_results: [
  { criterion: "secrets blocked from network calls", result: "PASS|FAIL", evidence: "..." },
  { criterion: "MCP data blocked from destructive ops", result: "PASS|FAIL", evidence: "..." },
  { criterion: "PII redaction in logs", result: "PASS|FAIL", evidence: "..." },
  { criterion: "clear error messages", result: "PASS|FAIL", evidence: "..." }
]
gaps_found: [...any issues discovered...]
```

