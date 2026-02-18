---
id: m-d3f3
status: closed
deps: []
created: 2026-02-17T23:13:04Z
type: task
priority: 2
assignee: Adam Avenir
updated: 2026-02-18T07:48:13Z
---
# Fold DEPENDENCIES.md into MODULES.md and IMPORTS.md

Consolidate /needs and module dependency architecture into canonical module/import docs.

## Acceptance Criteria

- Dependency semantics are covered in docs/dev/MODULES.md and/or docs/dev/IMPORTS.md.\n- docs/dev/DEPENDENCIES.md is ready for deletion by the remove ticket.


**2026-02-17 23:37 UTC:** Doc-audit input (source: `llm/run/doc-audit/tmp/results.json`) for this ticket.
This input MUST be verified with 100% confidence during implementation.

Files in scope to verify:
- `docs/dev/MODULES.md` [outdated]: Multiple file paths, line numbers, class names, and interface signatures are incorrect or no longer match the current codebase structure
- `docs/dev/IMPORTS.md` [mostly-current]: Architecture and component names are accurate, but several code examples use wrong function/class signatures and the circular import detection description misrepresents the implementation

Verification bar (required):
- Confirm or disprove each finding for the files above against current code/docs with 100% confidence.
- If disproved, record why with concrete file references.
- If confirmed, update docs in this ticket scope and keep links/frontmatter consistent.

**2026-02-18 00:15 UTC:** Verified against current code. Decisive consolidation list for folding `docs/dev/DEPENDENCIES.md` into `docs/dev/MODULES.md` + `docs/dev/IMPORTS.md`:

## `docs/dev/MODULES.md`

- Add a canonical `/needs` section (migrated from `DEPENDENCIES.md`) that matches current parser + evaluator behavior:
  - `/needs` is optional (do not keep wording that all modules must declare it).
  - Package ecosystems: `node/js`, `python/py`, `ruby/rb`, `go`, `rust`.
  - Boolean capabilities: `sh/bash`, `network/net`, `filesystem/fs`.
  - `cmd` supports wildcard (`*`), list, and map forms (with methods/subcommands/flags), plus bare command entries collected as command needs.
  - Keep examples aligned to this shape and current normalization in `core/policy/needs.ts`.
- Add import-time dependency enforcement details:
  - `ModuleContentProcessor` returns `moduleNeeds`.
  - `ImportDirectiveEvaluator.validateModuleResult()` enforces via `ModuleNeedsValidator.enforceModuleNeeds()`.
  - `DirectoryImportHandler` enforces child `index.mld` needs via the same validator callback.
  - Failure surface is `MlldImportError` with `code: NEEDS_UNMET` and per-capability detail lines.
- Fix confirmed outdated module-architecture references:
  - Replace `interpreter/eval/directives/import/*` paths with `interpreter/eval/import/*`.
  - Replace `interpreter/eval/directives/export.ts` with `interpreter/eval/export.ts`.
  - Replace `core/resolvers/HttpResolver.ts` with `core/resolvers/HTTPResolver.ts`.
  - Remove/replace reference to nonexistent `interpreter/eval/template-renderer.ts`.
- Correct stale ownership/signature descriptions in MODULES:
  - Import-collision tracking is not on `Environment.trackImportedBinding`; it is enforced by `ImportBindingGuards` (`ensureImportBindingAvailable`, `setVariableWithImportBinding`).
  - Lock-version enforcement attribution belongs to `ModuleImportHandler.validateLockFileVersion()` (called from `evaluateModuleImport()`), not `ImportDirectiveEvaluator`.
  - Resolver interface snippet must reflect current `core/resolvers/types.ts` `Resolver`/`ResolverCapabilities` contracts, not the old `IResolver` shape (`shouldHandle`, bare `priority` field).
- Update module type table to match current `MODULE_TYPE_PATHS` in `core/registry/types.ts` by adding `environment` (`.mlld/env`).
- Remove the stale gotcha claiming `cli/commands/info.ts` uses mock registry data; current implementation fetches real registry JSON.

## `docs/dev/IMPORTS.md`

- Add/expand a focused “Import-time /needs enforcement” section sourced from `DEPENDENCIES.md`, tied to current import orchestration (`ImportDirectiveEvaluator`, `ModuleNeedsValidator`, `DirectoryImportHandler`).
- Keep dependency text consistent with current import flow: needs are enforced after module processing and before binding/usage.
- Carry forward the already-verified IMPORTS drift fixes (same audit scope) while folding dependencies content:
  - path-resolution + import-type flow/signatures (`resolveImportPath` then `resolveImportType`), including `resolution.type` support for `node`.
  - circular-import description must use `ImportResolver` `Set<string>` tracking + `ModuleContentProcessor` global stack guard, not a local array stack on `ImportSecurityValidator`.
  - `MlldImportError` constructor/examples must use `new MlldImportError(message, options)`.
  - binding collision helper ownership must be `ImportBindingGuards`.
  - lock-version enforcement attribution must be `ModuleImportHandler.validateLockFileVersion`.
  - import type list should include `templates`.

## `docs/dev/DEPENDENCIES.md`

- After the above migration, reduce to a short pointer stub and mark ready for deletion by the remove ticket.
- Do not preserve duplicate standalone dependency semantics once MODULES/IMPORTS are canonical.
