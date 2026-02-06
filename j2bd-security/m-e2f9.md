---
id: m-e2f9
status: closed
deps: []
created: 2026-02-04T22:36:09Z
type: task
priority: 2
assignee: Adam
tags: [phase-4, adversarial]
updated: 2026-02-04T22:41:47Z
---
# Phase 4: Adversarial verification of security protections

Red team the security protections implementation to prove exit criteria hold.

## Exit Criteria to Verify

1. **API keys marked as secrets can never leak to network calls** - Run protection-1-secret.mld and verify denial. Then try to bypass: template injection, nested exe calls, indirect references.

2. **Data from MCP tools can't be used in destructive operations** - Verify protection-2-mcp.mld documentation is accurate about the design limitation. Confirm source labels can't be faked. This is EXPECTED to be a documentation item, not a runtime test.

3. **PII can't be logged or displayed without redaction** - Run protection-3-pii.mld and verify redaction. Then try to bypass: direct string access, object embedding, label stripping.

4. **Clear error messages when blocked** - Run protection-4-errors.mld and verify error includes: operation type, blocker identification, input labels, reason, suggestions.

## Adversarial Approach

For each protection:
1. Verify the happy path works (protection triggers)
2. Attempt at least 2 bypass techniques
3. Document what was tried and results

## Expected Result

Return status: 'verified' with exit_criteria_results array showing PASS/FAIL for each of the 4 criteria. If any FAIL, document the gap clearly.

## Files to Test

- docs/src/atoms/security/examples/protection-1-secret.mld
- docs/src/atoms/security/examples/protection-2-mcp.mld
- docs/src/atoms/security/examples/protection-3-pii.mld
- docs/src/atoms/security/examples/protection-4-errors.mld


**2026-02-04 22:41 UTC:** 7 additional bypass tests run: 6 PASS, 1 PARTIAL (regex edge case). Ready for Phase 5 final review.
