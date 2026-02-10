---
id: m-9a4c
status: closed
deps: []
created: 2026-02-09T14:30:47Z
type: task
priority: 2
assignee: Adam Avenir
tags: [urgency-high, phase-5, quick-fix]
updated: 2026-02-09T14:33:57Z
---
# Fix guards-privileged atom accuracy + main.mld guard syntax

Final review found two issues preventing 'solid' rating. Both are small targeted fixes.

## Fix 1: guards-privileged.md — line 39 accuracy

File: docs/src/atoms/security/guards-privileged.md

Current line 39:
```
Non-privileged guards attempting these on protected labels (`secret`, `untrusted`, `src:*`) get `PROTECTED_LABEL_REMOVAL` error.
```

This implies non-protected labels CAN be removed without privilege. The code at interpreter/hooks/guard-utils.ts:53 blocks ALL removal: `if (removeLabels.length > 0 && guard.privileged !== true)`. Protected labels get PROTECTED_LABEL_REMOVAL; non-protected labels get LABEL_PRIVILEGE_REQUIRED.

Replace line 39 with something like:
```
Non-privileged guards cannot remove ANY labels. Protected labels (`secret`, `untrusted`, `src:*`) produce `PROTECTED_LABEL_REMOVAL`; other labels produce `LABEL_PRIVILEGE_REQUIRED`. This ensures only policy-controlled code paths can modify label state.
```

Also update the contrast section header (line 41) from 'cannot remove protected labels' to 'cannot remove labels' since the restriction applies to all labels.

## Fix 2: main.mld — line 102 guard syntax

File: llm/run/j2bd/security/impl/main.mld

Current line 102:
```
guard @ensureVerified for llm = when [
```

Change to:
```
guard @ensureVerified after llm = when [
```

The `for` syntax defaults to `before` timing, but checking @mx.tools.calls requires `after` timing (post-execution state). The atoms all use `after llm`. Match them.

Both fixes are one-line changes. Run `npm run test:case -- security` after to verify no regressions.


**2026-02-09 14:33 UTC:** Fixes verified via worker output: guards-privileged.md line 39 reworded to state non-privileged guards cannot remove ANY labels (protected → PROTECTED_LABEL_REMOVAL, others → LABEL_PRIVILEGE_REQUIRED). main.mld guard changed to 'after llm' matching atoms.
