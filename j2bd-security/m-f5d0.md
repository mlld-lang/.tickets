---
id: m-f5d0
status: closed
deps: [m-b130]
created: 2026-02-09T12:44:38Z
type: task
priority: 2
assignee: Adam Avenir
tags: [excellence, high-polish]
updated: 2026-02-09T13:20:12Z
---
# Excellence assessment: defaults-rules two-step pattern

Evaluate the end-to-end user experience for the defaults-rules two-step pattern feature.

## Context

This job delivered:
- 4 documentation atoms updated: labels-sensitivity, labels-trust, policy-operations, policies
- 1 implementation artifact: llm/run/j2bd/security/impl/defaults-rules.mld
- 3 commits: d0c077bd4, 59844d072, ef0300f1a (base: 57b035f1d)
- Taint propagation fix for when-expressions and for-loops
- Operations mapping support in guards for two-step pattern

## What to Assess

1. **New user success path**: Read only the atoms (labels-sensitivity, labels-trust, policy-operations, policies, policy-label-flow) and the defaults-rules.mld artifact. Could a new user understand and implement the two-step pattern from these alone?

2. **Example clarity**: Are the examples in atoms self-contained? Do they show realistic scenarios? Is the progression logical?

3. **Error experience**: Run the defaults-rules.mld artifact. When rules block flows, do error messages explain: (a) which rule fired, (b) why the flow was blocked, (c) how to fix it?

4. **Getting started**: What's the minimum configuration needed? Is it clear from the docs?

5. **Common mistakes**: Would a user likely try `exe exfil @fn()` directly? Do docs adequately explain why the two-step pattern is preferred?

6. **Friction points from earlier phases**: (a) denied => handler pattern is used in the artifact but not documented in standalone atoms — is this a gap? (b) Are the atom cross-references (related fields) correct and helpful?

## Files to Review

- docs/src/atoms/security/labels-sensitivity.md
- docs/src/atoms/security/labels-trust.md
- docs/src/atoms/security/policy-operations.md
- docs/src/atoms/security/policies.md
- docs/src/atoms/security/policy-label-flow.md
- llm/run/j2bd/security/impl/defaults-rules.mld

## Rating Scale

- **Delightful**: A new user would succeed easily and recommend the feature
- **Solid**: A new user would succeed with minimal confusion
- **Adequate**: A new user would succeed but might get confused at points
- **Needs work**: A new user would struggle without additional help


**2026-02-09 13:20 UTC:** Final state: Error messages are best-in-class (structured with rule name, labels, reason, actionable suggestions). All atom examples now use functional invocation syntax. Cross-references corrected. Complete example section added to policy-operations.md. Label-specific suggestion text in PolicyEnforcer.ts. The two-step pattern (semantic labels + policy classification) is fully documented, implemented, tested, and polished.
