---
id: m-67de
status: done
deps: []
created: 2026-02-15T15:48:36Z
type: task
priority: 1
assignee: Adam
tags: [qa-2026-02-14, p1, reconcile, security]
updated: 2026-02-15T15:55:00Z
---
# Reconcile: defaults.unlabeled - verify it works, restore if yes, fix if no

Agent removed defaults.unlabeled from all docs claiming it doesn't work. Review agent says it IS implemented (PolicyEnforcer.applyDefaultTrustLabel, input-taint.ts). Spec shows it as optional policy config. QA found it broken.

Reconcile:
1. Write test case for defaults.unlabeled: untrusted - does unlabeled data get blocked by deny rules?
2. If YES: restore docs (but note it's opt-in, not default behavior)
3. If NO: investigate why QA and implementation disagree

Note: spec shows it's an option in policy config, not a default-on behavior. If we restore docs, clarify it's opt-in.

## Acceptance Criteria

defaults.unlabeled behavior verified with test, docs match reality

## Resolution

**Verified: `defaults.unlabeled` works correctly.** The previous agent's claim that it was broken was wrong.

### Tests added

Two new fixture tests confirm end-to-end behavior:

1. `tests/cases/exceptions/security/defaults-unlabeled-untrusted-deny/` -- file-loaded data (no user labels) gets auto-labeled `untrusted` by `defaults.unlabeled: "untrusted"`, then blocked by the `no-untrusted-destructive` built-in rule.

2. `tests/cases/exceptions/security/defaults-unlabeled-untrusted-label-deny/` -- same auto-labeling, but blocked by a custom `labels.untrusted.deny` rule instead of a built-in rule.

Both pass. The existing test `tests/cases/security/defaults-unlabeled-trust-label/` (verifying the label appears on `.mx.labels`) also still passes.

### Docs restored

- `docs/src/atoms/security/labels-trust.md` -- added "Opt-in auto-labeling" section explaining `defaults.unlabeled` with example and explicit note that it is opt-in, not default behavior.
- `docs/src/atoms/security/policies.md` -- added `unlabeled` to the `defaults` description.
- `CHANGELOG.md` -- corrected the misleading "docs cleanup" entry to "docs restored".

