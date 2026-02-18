---
id: m-7add
status: closed
deps: []
created: 2026-02-17T23:08:22Z
type: task
priority: 2
assignee: Adam Avenir
updated: 2026-02-18T07:26:08Z
---
# Docs consolidation: merge EXEC-VARS.md and VAR-EVALUATION.md

Unify executable-variable behavior and /var evaluation architecture into a single coherent doc surface.\n\nGoal:\n- Keep one canonical doc for variable/executable evaluation flow.\n- Preserve any unique low-level details currently only in VAR-EVALUATION.md.\n- Remove duplicated semantics and examples that conflict.

## Acceptance Criteria

- One canonical variable/evaluation doc path is selected and documented.\n- EXEC-VARS and VAR-EVALUATION no longer duplicate each other.\n- Cross-links from INTERPRETER.md and PIPELINE.md point to canonical sections.


**2026-02-17 23:37 UTC:** Doc-audit input (source: `llm/run/doc-audit/tmp/results.json`) for this ticket.
This input MUST be verified with 100% confidence during implementation.

Files in scope to verify:
- `docs/dev/EXEC-VARS.md` [outdated]: Doc uses deleted @exec directive throughout; references two non-existent files; code snippet attribution is wrong
- `docs/dev/VAR-EVALUATION.md` [mostly-current]: Accurate overall, but missing collection-evaluator.ts module and incomplete node type list for execution-evaluator

Verification bar (required):
- Confirm or disprove each finding for the files above against current code/docs with 100% confidence.
- If disproved, record why with concrete file references.
- If confirmed, update docs in this ticket scope and keep links/frontmatter consistent.

**2026-02-17 23:58 UTC:** Verified against current code/docs (100% confidence). Decisive update list:

- Canonical doc decision
  - Use `docs/dev/VAR-EVALUATION.md` as the single canonical doc for `/var` + executable-variable evaluation flow.
  - Merge only still-valid executable-variable semantics from `docs/dev/EXEC-VARS.md` into `VAR-EVALUATION.md`; then retire `EXEC-VARS.md`.

- `docs/dev/EXEC-VARS.md` findings (all confirmed)
  - Uses deleted `@exec` directive syntax throughout; current grammar uses `/exe`/`exe` (`grammar/directives/exe.peggy`).
  - References non-existent files:
    - `grammar/directives/exec.peggy` (actual file is `grammar/directives/exe.peggy`).
    - `interpreter/eval/add.ts` (not present).
  - Snippet attribution is wrong:
    - “executable variables return variable itself for lazy execution” is currently implemented in `interpreter/eval/data-values/VariableReferenceEvaluator.ts` (not `data-value-evaluator.ts`).

- `docs/dev/VAR-EVALUATION.md` findings
  - Confirmed missing module boundary entry for `interpreter/eval/var/collection-evaluator.ts`.
    - This module is actively used by `rhs-dispatcher.ts` and `variable-builder.ts` for object/array complexity detection and collection item evaluation.
  - Confirmed incomplete execution-node list for `execution-evaluator.ts`.
    - Current `isExecutionValueNode` includes: `code`, `command`, `ExecInvocation`, `ExeBlock`, `WhenExpression`, `ForExpression`, `LoopExpression`, `foreach`, `foreach-command`, `NewExpression`, and `Directive` with `kind === 'env'`.

- Merge content into `docs/dev/VAR-EVALUATION.md`
  - Add a concise executable-variable semantics subsection:
    - `/exe` defines executable variables.
    - `@fn` = executable reference; `@fn(...)` = invocation via `interpreter/eval/exec-invocation.ts`.
    - Lazy reference behavior in value contexts maps to `VariableReferenceEvaluator` executable pass-through.
  - Expand module-boundary section to include `collection-evaluator.ts` responsibilities.
  - Update `execution-evaluator.ts` node coverage list to match code exactly.

- Cross-link updates (acceptance requirement)
  - `docs/dev/INTERPRETER.md`: remove `docs/dev/EXEC-VARS.md` from related-docs and keep/link `docs/dev/VAR-EVALUATION.md` as canonical.
  - `docs/dev/PIPELINE.md`: add or update a pointer to canonical variable/executable evaluation docs (`docs/dev/VAR-EVALUATION.md`) where executable invocation semantics are discussed.

- `docs/dev/EXEC-VARS.md`
  - After merge, ready for deletion.

- Do NOT carry forward
  - Any `@exec` / `@data` directive examples.
  - References to `exec.peggy` or `add.ts`.
  - Outdated snippet/file attributions that no longer match implementation.
