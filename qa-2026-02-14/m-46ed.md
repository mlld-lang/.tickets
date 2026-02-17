---
id: m-46ed
status: done
deps: []
created: 2026-02-15T15:48:51Z
type: task
priority: 1
assignee: Adam
tags: [qa-2026-02-14, p1, reconcile, guards]
updated: 2026-02-15T15:59:00Z
---
# Reconcile: guard transform chaining - verify spec vs implementation vs docs

## Finding: before-phase is last-wins, not chaining

Empirical test with 3 before guards for op:exe (`allow "G1"`, `allow "G2"`, `allow "G3"`) produces output `G3`, confirming last-wins.

### Implementation details

**perInput scope** (label-targeted guards): `guard-pre-hook.ts` lines 89-123. Each guard evaluates against the original `candidate.variable`. `currentInput` is overwritten by each replacement but never fed back as input to subsequent guards. Last replacement used.

**perOperation scope** (op:exe guards): `guard-pre-hook.ts` lines 126-161. The operation snapshot IS rebuilt after each replacement (`opSnapshot = buildOperationSnapshot(transformedInputs)`), so the next guard's `@input` contains the previous guard's Variable objects. However, when guards use static replacements, the effect is last-wins. When guards reference `@input` in templates, the Variable objects serialize as JSON rather than resolving to string values.

**After-phase**: Chains correctly. Both perInput and perOperation post-decision engines update `currentInput`/`activeOutputs` and pass transformed values to subsequent guards.

### What was done

1. Test written: `tests/cases/feat/security/guard-before-transform-chain/` -- 3 before op:exe guards with static replacements, output is "G3" (last-wins confirmed)
2. Docs updated: `guard-composition.md` line 4 clarified to explicitly say "last-wins" with explanation that each guard evaluates against the original input independently
3. Spec divergence noted: `todo/spec-security.md` annotated with DIVERGENCE note at the chaining semantics section, referencing m-46ed

### Spec divergence (for later resolution)

`todo/spec-security.md` says "Output of guard N becomes input of guard N+1" and "transforms chain sequentially" without distinguishing phases. Implementation is last-wins for before-phase, chaining for after-phase. The spec needs to be updated to reflect this phase distinction.

## Acceptance Criteria

Guard chaining behavior verified, docs and spec aligned

