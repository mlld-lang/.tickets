---
id: m-ea2a
status: closed
deps: []
created: 2026-02-17T23:13:04Z
type: task
priority: 2
assignee: Adam Avenir
updated: 2026-02-18T07:53:39Z
---
# Fold CONTENT-LOADER-REFACTOR-SEAMS.md into ALLIGATOR.md and INTERPRETER.md

Move current content-loader seam details into canonical docs/dev/ALLIGATOR.md and docs/dev/INTERPRETER.md.

## Acceptance Criteria

- Content-loader seam architecture is documented in canonical docs.\n- Refactor-only/stale notes are removed.\n- docs/dev/CONTENT-LOADER-REFACTOR-SEAMS.md is ready for deletion by the remove ticket.


**2026-02-17 23:37 UTC:** Doc-audit input (source: `llm/run/doc-audit/tmp/results.json`) for this ticket.
This input MUST be verified with 100% confidence during implementation.

Files in scope to verify:
- `docs/dev/ALLIGATOR.md` [outdated]: Several examples use deprecated top-level .text/.data access and non-existent .mx.fileCount; detection rule omits that / is not actually a trigger character
- `docs/dev/INTERPRETER.md` [mostly-current]: Core architecture is accurately described; Quick Map is missing several directives (bail, env, loop) and misidentifies /log's output destination as stdout instead of stderr

Verification bar (required):
- Confirm or disprove each finding for the files above against current code/docs with 100% confidence.
- If disproved, record why with concrete file references.
- If confirmed, update docs in this ticket scope and keep links/frontmatter consistent.

**2026-02-18 00:25 UTC:** Verified against current code/docs. Decisive consolidation list for folding `docs/dev/CONTENT-LOADER-REFACTOR-SEAMS.md` into canonical docs:

## `docs/dev/ALLIGATOR.md`

- Fix Detection Rules trigger set for interpolation contexts:
  - remove `/` from trigger list.
  - document trigger as `.`, `*`, `@` (matches grammar helpers and file-reference interpolation lookahead).
- Replace stale glob-count examples:
  - `@docs.mx.fileCount` -> `@docs.mx.length` (both occurrences).
- Correct wrapper access examples in **mlld field-access contexts**:
  - do not document wrapper-level `@pkg.data` / `@pkg.text` / `@logs.data[...]` / `@logs.text` as canonical mlld access.
  - use direct parsed access (`@pkg.name`, `@logs[0].message`) or explicit wrapper views via `@pkg.mx.data` / `@pkg.mx.text`.
- Keep `.keep` guidance for JS/Node and do **not** remove wrapper-object semantics there:
  - StructuredValue objects still have `.text`/`.data` at runtime when preserved with `.keep`; the correction is about mlld field-access docs, not JS runtime shape.
- Add a short pointer from ALLIGATOR to INTERPRETER for internal content-loader seam architecture (to avoid reintroducing a separate seam doc).

## `docs/dev/INTERPRETER.md`

- Quick Map updates (confirmed missing):
  - add `/bail` -> `interpreter/eval/bail.ts`
  - add `/env` -> `interpreter/eval/env.ts`
  - add `/loop` -> `interpreter/eval/loop.ts`
- Correct `/log` Quick Map wording:
  - current doc says stdout shorthand; runtime path is stderr-directed (log sugar routes to stderr target; pipeline `log` effect emits `stderr`).
- Add a compact “Content Loader Seams” architecture subsection migrated from `CONTENT-LOADER-REFACTOR-SEAMS.md`, aligned to current modules:
  - composition/entrypoint: `interpreter/eval/content-loader.ts`
  - orchestration router: `interpreter/eval/content-loader/orchestrator.ts`
  - branch boundaries: AST (`ast-pattern-resolution.ts`, `ast-variant-loader.ts`), URL (`url-handler.ts`), glob (`glob-loader.ts`), single-file (`single-file-loader.ts`)
  - shared helpers: `source-reconstruction.ts`, `section-utils.ts`, `transform-utils.ts`, `policy-aware-read.ts`, `security-metadata.ts`
  - finalization boundary: `finalization-adapter.ts` with `wrapLoadContentValue`-based StructuredValue normalization and metadata merge
  - optional/error behavior to preserve: optional glob -> empty array wrapper, optional single-file miss -> null, `MlldSecurityError` passthrough

## `docs/dev/CONTENT-LOADER-REFACTOR-SEAMS.md`

- After migrating the canonical architecture content above, reduce this file to a short pointer stub and mark ready for deletion by the remove ticket.
- Do not keep refactor-phase-only checklist language (for example, “PHASE-0 BASELINE COMBINATIONS”) in canonical docs unless intentionally moved to test planning docs.
