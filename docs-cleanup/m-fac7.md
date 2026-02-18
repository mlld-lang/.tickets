---
id: m-fac7
status: closed
deps: []
created: 2026-02-17T23:13:04Z
type: task
priority: 2
assignee: Adam Avenir
updated: 2026-02-18T07:56:47Z
---
# Fold GRAMMAR-old.md into GRAMMAR.md

Migrate any still-relevant guidance from docs/dev/GRAMMAR-old.md into docs/dev/GRAMMAR.md and remove duplication.

## Acceptance Criteria

- docs/dev/GRAMMAR.md contains any still-relevant content from docs/dev/GRAMMAR-old.md.\n- Historical/deprecated guidance is excluded or condensed to a short note if still needed.\n- docs/dev/GRAMMAR-old.md is ready for deletion by the remove ticket.


**2026-02-17 23:37 UTC:** Doc-audit input (source: `llm/run/doc-audit/tmp/results.json`) for this ticket.
This input MUST be verified with 100% confidence during implementation.

Files in scope to verify:
- `docs/dev/GRAMMAR.md` [mostly-current]: Core architecture is accurate, but Critical Patterns lists two removed/nonexistent abstractions, and Block Reparse section omits several allowed start rules added since the doc was written.

Verification bar (required):
- Confirm or disprove each finding for the files above against current code/docs with 100% confidence.
- If disproved, record why with concrete file references.
- If confirmed, update docs in this ticket scope and keep links/frontmatter consistent.

**2026-02-18 00:28 UTC:** Verified against current grammar sources/build config. Decisive migration list for folding `docs/dev/GRAMMAR-old.md` into `docs/dev/GRAMMAR.md`:

## `docs/dev/GRAMMAR.md`

- Keep GRAMMAR.md as canonical, but fix confirmed drift in “Critical Patterns” and related guidance.
- Replace/remove two stale abstractions currently documented as critical patterns:
  - `WrappedPathContent` is removed (explicitly marked removed in `grammar/patterns/content.peggy`); replace references with current path/content entry points (for example `PathExpression` and Alligator/file-reference patterns).
  - `GenericList` does not exist in current grammar (Peggy parametric-rule approach is not used); document current list strategy (`CommaSpace` + rule-specific first/rest list rules in `grammar/patterns/lists.peggy`).
- Update all lingering GenericList guidance in GRAMMAR.md (not just the one “Critical Patterns” list item) so docs do not instruct use of nonexistent abstractions.
- Expand the Block Reparse section to reflect current start-rule coverage:
  - currently reparsed/wired by grammar fallbacks include `ExeBlockBody`, `ForBlockBody`, `LoopBlockBody`, `WhenConditionList`, `WhenExpressionConditionList`, `WhenBoundExpressionConditionList`, `GuardRuleList`, `WhenActionBlockContent`, and `IfActionBlockBody`.
  - align the “allowed start rules” note with current `allowedStartRules` in `grammar/build-grammar.mjs` (which also includes entries such as `ForBlockStatementList`, `WhenActionBlockBody`, `TemplateBodyAtt`, `TemplateBodyMtt`, and `Start`).
- Keep the existing accurate principles (build flow, helper source-of-truth in `grammar/deps`, type synchronization) and avoid reintroducing outdated directive-era terminology.

## `docs/dev/GRAMMAR-old.md`

- Migrate only still-relevant meta-guidance that is not already present in GRAMMAR.md (if any concise phrasing is worth keeping).
- Do **not** migrate historical/deprecated parse trees and directive walkthroughs tied to removed syntax (e.g., old `@text/@data/@add/@exec` era material).
- After migration, reduce to a pointer stub and mark ready for deletion by the remove ticket.
