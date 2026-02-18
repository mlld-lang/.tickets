---
id: m-f97e
status: closed
deps: []
created: 2026-02-17T23:08:22Z
type: task
priority: 2
assignee: Adam Avenir
updated: 2026-02-18T07:55:44Z
---
# Docs consolidation: merge MODULES.md and IMPORTS.md

Consolidate overlapping import/module architecture docs into one canonical developer doc.\n\nTarget:\n- Keep docs/dev/MODULES.md as canonical (or rename, but end state is one primary module/import architecture doc).\n- Merge unique, still-correct content from docs/dev/IMPORTS.md.\n- Keep docs/dev/RESOLVERS.md separate and focused on resolver internals/capabilities.

## Acceptance Criteria

- One canonical module/import architecture doc remains.\n- Overlap between MODULES and IMPORTS is removed.\n- Resolver-specific detail stays in docs/dev/RESOLVERS.md with links from canonical doc.\n- Frontmatter related-docs/related-code updated accordingly.


**2026-02-17 23:37 UTC:** Doc-audit input (source: `llm/run/doc-audit/tmp/results.json`) for this ticket.
This input MUST be verified with 100% confidence during implementation.

Files in scope to verify:
- `docs/dev/MODULES.md` [outdated]: Multiple file paths, line numbers, class names, and interface signatures are incorrect or no longer match the current codebase structure
- `docs/dev/IMPORTS.md` [mostly-current]: Architecture and component names are accurate, but several code examples use wrong function/class signatures and the circular import detection description misrepresents the implementation
- `docs/dev/RESOLVERS.md` [outdated]: Significant inaccuracies: wrong resolver names, non-existent classes/interfaces (MCPResolver, TTLCacheService as described, resolveAtReference, joinPathSegments), wrong file name for lock/config files, wrong capabilities for @now, wrong ProjectPathResolver name and capabilities, and wrong ResolverError constructor signature

Verification bar (required):
- Confirm or disprove each finding for the files above against current code/docs with 100% confidence.
- If disproved, record why with concrete file references.
- If confirmed, update docs in this ticket scope and keep links/frontmatter consistent.

**2026-02-18 00:27 UTC:** Verified against current docs/code. Decisive consolidation plan for merging `docs/dev/MODULES.md` + `docs/dev/IMPORTS.md` while keeping resolver internals in `docs/dev/RESOLVERS.md`:

## `docs/dev/MODULES.md` (keep as canonical)

- Keep `MODULES.md` as the single canonical module/import architecture doc; fold in only still-correct, non-duplicate import architecture sections from `IMPORTS.md`.
- Fix confirmed path/name/signature drift in existing MODULES content:
  - replace stale `interpreter/eval/directives/import/*` paths with `interpreter/eval/import/*` (frontmatter + body).
  - update entrypoint references to current import composition rooted at `interpreter/eval/import/ImportDirectiveEvaluator.ts`.
  - normalize resolver class naming to `HTTPResolver` (remove `HttpResolver` references).
  - replace stale `IResolver { resolve(spec, mx), shouldHandle(...) }` snippet with current `Resolver` interface shape from `core/resolvers/types.ts`.
- Correct lock/version validation ownership and call path:
  - lock-version mismatch enforcement is in `interpreter/eval/import/ModuleImportHandler.ts` (`validateLockFileVersion`), not in `ImportDirectiveEvaluator`.
- Correct import-binding collision references:
  - current binding flow uses `ImportBindingGuards` + `Environment.setImportBinding(...)`; do not reference `Environment.trackImportedBinding`.
- Remove/replace nonexistent template interpolation file references:
  - do not reference `interpreter/eval/template-renderer.ts`; use current exec/import capture paths (`interpreter/eval/import/VariableImporter.ts`, `interpreter/eval/exec-invocation.ts`, exec handler modules).
- Merge in the accurate “Evaluator Composition Boundaries” model from `IMPORTS.md` (ImportDirectiveEvaluator + router + handlers + validators) to replace stale MODULES orchestration prose.
- Keep resolver section in MODULES high-level only and explicitly link to `docs/dev/RESOLVERS.md` for resolver internals.

## `docs/dev/IMPORTS.md` (de-duplicate into MODULES)

- Migrate unique, still-correct architecture material into MODULES, then reduce IMPORTS to pointer/stub.
- For migrated examples/snippets, fix confirmed signature/implementation drift:
  - `ImportPathResolver.resolveImportPath(directive)` (not `resolveImportType(path)`).
  - lock-file version validation flow belongs to module import handler path, not `ImportDirectiveEvaluator.evaluateModuleImport()`.
  - circular import detection description must not present `ImportSecurityValidator` as owning a private stack array; actual tracking is environment/global-stack based (`env.isImporting`, `beginImport/endImport`, plus ModuleContentProcessor global tracking guard).
- Keep architecture/component naming that is still accurate (ImportDirectiveEvaluator, ImportPathResolver, ModuleContentProcessor, VariableImporter, ImportSecurityValidator), but remove duplicated narrative once merged into MODULES.

## `docs/dev/RESOLVERS.md` (keep separate; do not merge internals from it into MODULES now)

- Audit finding confirmed: current RESOLVERS doc contains multiple major inaccuracies (resolver interfaces/classes/methods and capability details do not match current resolver code).
- For this consolidation ticket, keep RESOLVERS separate and referenced; do not copy resolver-internal details from RESOLVERS into MODULES until RESOLVERS is corrected.
- Ensure MODULES links RESOLVERS as the resolver-internals doc, with resolver specifics to be corrected in the resolver-focused follow-up work.
