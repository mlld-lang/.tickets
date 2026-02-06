---
id: m-afd7
status: open
deps: []
created: 2026-02-05T12:20:08Z
type: impl
priority: 1
assignee: Adam
tags: [phase-4, adversarial, urgency-high]
updated: 2026-02-05T12:20:11Z
---
# ## Objective

Red team the policy composition implementation. Try to break it.

## Exit Criteria to Prove

1. **Deny lists take precedence** - If team denies rm:* and local allows rm:*, the merged policy MUST still deny rm:*
2. **Allow lists intersect** - Only commands allowed by ALL policies appear in merged allow
3. **Label deny rules trigger** - If src:mcp data has deny: [op:cmd:git:push], attempting git push with that data MUST fail

## Adversarial Tests

### Test 1: Try to bypass deny via local override
- Create a script where team policy denies curl:*
- Local override tries to explicitly allow curl:*
- Verify merged policy still shows curl:* in deny list
- Attempt to run curl and confirm it fails

### Test 2: Try to sneak past allow intersection
- Team allows [git:*, npm:*]
- Project allows [git:*, node:*]
- Local allows [git:*, jq:*]
- Verify merged allow is ONLY git:* (common to all)
- Attempt to run npm and confirm it fails (not in intersection)

### Test 3: Label flow with composed policy
- Policy defines src:mcp: { deny: [op:cmd:git:push] }
- Load data with src:mcp source label
- Attempt to use that data in a git push command
- Verify the operation is blocked

### Test 4: Edge case - deny wins over allow at same specificity
- What if both deny and allow have cmd:git:status?
- Document the behavior (should be deny wins or error)

### Test 5: Run the example.md end-to-end
- Execute tests/cases/security/policy-composition-layers/example.md directly with mlld validate or interpreter
- Confirm output matches expected.md exactly

## Return Format

Return status: verified only if ALL exit criteria pass with evidence.
Return status: failed with specific failures if any test reveals a gap.

