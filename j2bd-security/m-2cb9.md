---
id: m-2cb9
status: closed
deps: []
created: 2026-02-09T16:57:38Z
type: task
priority: 2
assignee: Adam Avenir
tags: [phase-4, adversarial, urgency-high]
updated: 2026-02-09T17:46:12Z
---
# Adversarial verification of policy composition

Phase 4 adversarial verification. Red-team the policy composition feature to prove exit criteria hold.

Exit criteria to verify:
1. policy-composition atom explains union semantics - check atoms exist and are accurate
2. policy-capabilities atom covers allow/deny precedence - verify docs match implementation
3. policy-label-flow atom shows label denies in composed policy - verify docs match
4. Compose team, project, local policies - test 3-layer composition works
5. Allow/deny intersection on commands - verify enforcement, not just config
6. Combined label rules fire - verify label deny across composed policies
7. Deny lists take precedence in merged policy - confirm
8. Allow lists intersect for capabilities - confirm
9. Label deny rules still trigger - confirm
10. Target example pattern works: inline policy objects + multi-arg union(@team, @project, @local)

Key attack vectors:
- Try to bypass deny by adding an allow in another layer
- Test that 3-arg union actually merges all 3 (not just first 2)
- Test inline `policy @name = { ... }` followed by `policy @combined = union(@name1, @name2, @name3)`
- Verify that the exact target example code pattern (with @local variable name) works
- Check doc claims match actual behavior for intersection/union rules

Existing test cases to verify:
- tests/cases/security/policy-composition-allow-intersection/
- tests/cases/exceptions/security/policy-composition-deny-union/
- tests/cases/exceptions/security/policy-composition-label-deny/
- tests/cases/security/policy-composition-layers/


**2026-02-09 17:46 UTC:** Re-verified after remediation commit 07c12c488 (renamed @local→@devLocal in test case, completed cross-references). All exit criteria hold.
