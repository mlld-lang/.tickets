---
id: m-e2ed
status: closed
deps: []
created: 2026-02-19T21:56:23Z
type: task
priority: 0
assignee: Adam Avenir
updated: 2026-02-19T23:05:21Z
---
# Audit mlld validate warnings and hints against current docs and patterns

## Summary

mlld validate emits warnings and hints that may be outdated, overly cautious, or misaligned with current recommended patterns. Audit all warnings in cli/commands/analyze.ts against actual docs and usage, remove or rewrite ones that give bad advice.

## Trigger

The when-exe-implicit-return warning flags perfectly normal code as suspicious:

```mlld
exe @classify(task, agentList, defaultAgent) = [
  ...
  when !@result => { agent: @defaultAgent, confidence: "low", reasoning: "router failed" }
  => @result
]
```

Warning: "when action in an exe block returns from the exe when matched. Use block-form return to make return intent explicit."
Hint: "Use when @cond => [ => @value ] for explicit returns"

This is wrong. The implicit return from when inside exe is the natural, documented idiom. The suggestion to wrap it as [ => @value ] adds verbosity for no benefit. This warning should be removed entirely.

## All warning codes to audit

From cli/commands/analyze.ts, these warnings are emitted. Each needs to be checked against current docs and real usage:

| Code | Line | Message summary | Status |
|------|------|-----------------|--------|
| when-exe-implicit-return | ~737 | when action in exe block returns implicitly | REMOVE — this is normal idiomatic code |
| deprecated-json-transform | ~860 | @identifier is deprecated | Audit — check if deprecated items list is current |
| exe-parameter-shadowing | ~956 | Parameter can shadow caller variables | Audit — is this still a real concern? |
| template-strict-for-syntax | ~1241 | @for not valid in templates | Audit — wording |
| hyphenated-identifier-in-template | ~1287 | Hyphenated identifier parsing changed | Audit — is this migration warning still needed? |
| (suspicious field access) | ~443 | SUSPICIOUS_FIELD_ACCESS patterns | Audit — check the suspicious patterns list |
| (reserved/builtin conflicts) | ~524, ~532, ~567, ~575 | Variable shadows reserved/builtin name | Audit — check lists are current |
| (scope shadowing) | ~543, ~585 | Variable shadows outer scope | Audit — is this helpful or noisy? |
| (template unknown vars) | ~1356, ~1365 | Unknown variable in template | Audit — wording and accuracy |
| (@state anti-pattern) | ~666 | Local @state is mutable-state anti-pattern | Audit — is this still relevant? |

## Implementation approach

1. Read cli/commands/analyze.ts fully — understand each warning and when it fires
2. For each warning, check:
   - Does the flagged pattern appear in docs as recommended? If so, remove the warning.
   - Does the hint text match current docs language? If not, update it.
   - Is the warning still relevant or was it for a migration that's complete?
   - Does it fire on code in plugins/mlld/examples/ or llm/run/? If so, is that code wrong or is the warning wrong?
3. Remove when-exe-implicit-return entirely
4. Update or remove others as needed
5. Run mlld validate on plugins/mlld/examples/ and llm/run/ to check for remaining false positives

## Getting up to speed

- Warning implementation: cli/commands/analyze.ts (search for warnings.push)
- Current docs on when syntax: docs/src/atoms/syntax/ and docs/user/flow-control.md
- Example that triggers the bad warning: plugins/mlld/examples/event-agent/lib/router.mld line 19
- Run mlld validate on the examples to see all current warnings: mlld validate plugins/mlld/examples/**/*.mld

