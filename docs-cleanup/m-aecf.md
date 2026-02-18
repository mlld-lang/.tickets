---
id: m-aecf
status: closed
deps: []
created: 2026-02-17T23:13:04Z
type: task
priority: 2
assignee: Adam Avenir
updated: 2026-02-18T07:39:07Z
---
# Fold WITH.md into PIPELINE.md (plus INTERPRETER.md pointer)

Consolidate /with clause architecture into docs/dev/PIPELINE.md with only a concise pointer in docs/dev/INTERPRETER.md where needed.

## Acceptance Criteria

- /with architecture is canonical in docs/dev/PIPELINE.md.\n- docs/dev/INTERPRETER.md contains at most a pointer/reference.\n- docs/dev/WITH.md is ready for deletion by the remove ticket.


**2026-02-17 23:37 UTC:** Doc-audit input (source: `llm/run/doc-audit/tmp/results.json`) for this ticket.
This input MUST be verified with 100% confidence during implementation.

Files in scope to verify:
- `docs/dev/PIPELINE.md` [outdated]: Multiple interface signatures are wrong (RetryContext.allAttempts and PipelineState.allRetryHistory use StructuredValue[] not string[]), grammar file path is incorrect, old @exec/@data/@text directive syntax appears in examples, and the Available Transformers list is incomplete.
- `docs/dev/INTERPRETER.md` [mostly-current]: Core architecture is accurately described; Quick Map is missing several directives (bail, env, loop) and misidentifies /log's output destination as stdout instead of stderr

Verification bar (required):
- Confirm or disprove each finding for the files above against current code/docs with 100% confidence.
- If disproved, record why with concrete file references.
- If confirmed, update docs in this ticket scope and keep links/frontmatter consistent.

**2026-02-18 00:05 UTC:** Verified against current code. Use this as the decisive consolidation list.

## docs/dev/PIPELINE.md

- Make `PIPELINE.md` the canonical `/with` architecture doc and fold in relevant `WITH.md` content as a concise, code-accurate section.
- Keep only currently implemented `/with` behavior, sourced from grammar/eval:
  - grammar source of truth: `grammar/patterns/with-clause.peggy`
  - pipeline detection/metadata extraction: `interpreter/eval/pipeline/detector.ts`
  - with-clause application path: `interpreter/eval/with-clause.ts`
- In the folded `/with` section, document supported pipeline-related keys accurately:
  - `pipeline`, `format`, `parallel`, `delay` (stored as `delayMs`), `stream`, `streamFormat`
  - `asSection` support where applicable (`detector.ts`, `interpreter/eval/var/rhs-content.ts`)
- Do NOT carry `WITH.md`'s `with { needs: ... }` claim into canonical docs (no current grammar/evaluator support for `withClause.needs`).
- Do NOT carry speculative/future wording from `WITH.md` (e.g., future capability/security revamp notes).

- Confirmed audit fixes needed in `PIPELINE.md` (must be corrected while folding):
  - Retry types:
    - `RetryContext.allAttempts` is `StructuredValue[]` (not `string[]`) in `interpreter/eval/pipeline/state-machine.ts`
    - `PipelineState.allRetryHistory` is `Map<string, StructuredValue[]>` (not `Map<string, string[]>`)
  - Grammar/file path correction:
    - remove/replace stale grammar map that references nonexistent `grammar/directives/data.peggy`
    - current grammar layout uses `grammar/directives/var.peggy`, `grammar/directives/exe.peggy`, and patterns under `grammar/patterns/*`
  - Syntax modernization:
    - replace outdated examples using `@exec`, `@data`, `@text` with current `/exe` and `/var` syntax
  - Transformer list completion from `interpreter/builtin/transformers.ts`:
    - `typeof`, `exists`, `xml`, `parse` (+ `loose|strict|llm|fromlist`), `json` alias (+variants), `csv`, `md`, `upper`, `lower`, `trim`, `pretty`, `sort`

## docs/dev/INTERPRETER.md

- Keep only a concise pointer to `/with` canonical behavior in `docs/dev/PIPELINE.md`; avoid duplicating `/with` architecture details.
- Update Quick Map for correctness:
  - add missing directives: `/bail`, `/env`, `/loop` (present in `interpreter/eval/directive.ts` dispatch)
  - fix `/log` destination description: logs target `stderr`, not stdout
    - grammar sugar maps log to stderr target in `grammar/directives/output.peggy`
    - runtime pipeline log effect emits `stderr` in `interpreter/eval/pipeline/builtin-effects.ts`

## docs/dev/WITH.md

- After moving the verified `/with` content into `PIPELINE.md`, mark `WITH.md` ready for deletion.
- Ensure no canonical behavior remains only in `WITH.md`.
