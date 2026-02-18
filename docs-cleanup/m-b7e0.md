---
id: m-b7e0
status: closed
deps: []
created: 2026-02-17T23:08:22Z
type: task
priority: 2
assignee: Adam Avenir
updated: 2026-02-18T07:41:23Z
---
# Docs consolidation: fold ALLIGATOR.md into DATA.md

Treat alligator/content-loader behavior as part of data handling architecture to reduce fragmentation.\n\nScope:\n- Move authoritative alligator semantics and metadata behavior into docs/dev/DATA.md.\n- Keep brief pointer-level mention in docs/dev/INTERPRETER.md where useful.

## Acceptance Criteria

- DATA.md contains the canonical alligator/content-loading architecture section.\n- ALLIGATOR.md is removed or reduced to a short pointer stub (project choice, documented in ticket notes).\n- Related-docs/frontmatter updated to match chosen end state.


**2026-02-17 23:37 UTC:** Doc-audit input (source: `llm/run/doc-audit/tmp/results.json`) for this ticket.
This input MUST be verified with 100% confidence during implementation.

Files in scope to verify:
- `docs/dev/ALLIGATOR.md` [outdated]: Several examples use deprecated top-level .text/.data access and non-existent .mx.fileCount; detection rule omits that / is not actually a trigger character
- `docs/dev/DATA.md` [mostly-current]: Largely accurate; two medium-severity issues: incorrect file path/line reference for show.ts:630 (file is only 158 lines; pattern lives in show/show-invocation-handlers.ts:56), and the Pipelines section misattributes structuredOutputs/previousOutputs to PipelineExecutor when they actually live in state-machine.ts context building.

Verification bar (required):
- Confirm or disprove each finding for the files above against current code/docs with 100% confidence.
- If disproved, record why with concrete file references.
- If confirmed, update docs in this ticket scope and keep links/frontmatter consistent.

**2026-02-18 00:07 UTC:** Verified against current code. Use this as the decisive consolidation list.

## docs/dev/DATA.md

- Make `DATA.md` canonical for all alligator/content-loader architecture.
- Add a dedicated “Alligator / Angle-Bracket Content Loading” section that captures current behavior from code:
  - `<...>` detection in interpolation contexts is based on `.` / `*` / `@` triggers (not `/`), with HTML-comment/tag guards and escape handling (`grammar/patterns/file-reference.peggy`, `grammar/deps/grammar-core.ts:isFileReferenceContent`).
  - Wrapper access pattern is `.mx.*` for system metadata + wrapper views; do not document top-level `@val.text` / `@val.data` as canonical.
  - JSON/JSONL parse semantics and load metadata should be described from `interpreter/utils/load-content-structured.ts` and `interpreter/eval/content-loader/finalization-adapter.ts`.
  - Glob aggregate metadata should use current fields (`.mx.length`, file metadata per element), not `.mx.fileCount`.
  - Preserve `.keep` / `.keepStructured` boundary behavior for JS/Node handoff.

- Confirmed in-scope `DATA.md` fixes:
  - Replace invalid reference `interpreter/eval/show.ts:630` with the real location in `interpreter/eval/show/show-invocation-handlers.ts:56`.
  - In Pipelines section, re-attribute `previousOutputs` / `previousStructuredOutputs` / `structuredOutputs` context assembly to `interpreter/eval/pipeline/state-machine.ts` (`buildStageContext`), not `PipelineExecutor`.

## docs/dev/ALLIGATOR.md

- Current doc has outdated examples and should not remain authoritative:
  - deprecated top-level `.text` / `.data` access examples
  - nonexistent `.mx.fileCount` examples
  - detection rule incorrectly includes `/` as trigger
- Consolidation end-state choice: retire ALLIGATOR as standalone architecture doc and replace with either:
  - short pointer stub to `docs/dev/DATA.md`, or
  - full deletion once links/frontmatter are updated.
- Recommended end state for fragmentation reduction: pointer stub or deletion (no duplicate architecture content).

## Related-docs/frontmatter alignment

- If ALLIGATOR is deleted or reduced to pointer, update DATA/INTERPRETER related-docs links so DATA is the canonical target for content-loader/alligator behavior.
