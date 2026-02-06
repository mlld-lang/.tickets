---
id: m-8cbd
status: closed
deps: []
created: 2026-02-05T12:12:48Z
type: task
priority: 2
assignee: Adam
tags: [phase-3, verification]
updated: 2026-02-05T12:19:56Z
---
# Verify policy composition exit criteria

## Objective

Verify Phase 3 exit criteria for policy composition are met.

## Exit Criteria to Verify

1. **Deny lists take precedence in merged policy** - When team denies `rm:*` and project denies `curl:*`, both should be blocked in the final composed policy
2. **Allow lists intersect for capabilities** - Only commands allowed by ALL policies should work (team allows git+npm+echo, project allows node+git+echo, local allows jq+git+echo → only git+echo should pass)
3. **Label deny rules still trigger** - Project policy has `secret: { deny: ["exfil"] }` which should still apply in composed policy

## Verification Approach

1. Run the existing test case at `tests/cases/security/policy-composition-layers/` and confirm output matches expected
2. Verify the test output shows:
   - Allow intersection: `["git:*", "echo"]`
   - Deny union: `["rm:*", "curl:*"]`
   - Minimum limits: `5000`
3. Check that the label rules are preserved in composed policy (may need to add `/show @mx.policy.configs.labels` to verify)

## Success Criteria

All three exit criteria must pass with concrete evidence from test output.

