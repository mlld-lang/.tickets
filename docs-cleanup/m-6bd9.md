---
id: m-6bd9
status: closed
deps: [m-fac7, m-3d75, m-4a89, m-3d24, m-b453, m-953a, m-ea2a, m-aecf, m-8b23, m-e7fc, m-d3f3, m-fc23, m-4766, m-6feb, m-d002, m-e338]
created: 2026-02-17T23:08:09Z
type: task
priority: 1
assignee: Adam Avenir
updated: 2026-02-18T08:03:02Z
---
# Docs cleanup: fold and remove obsolete standalone dev docs

Remove obsolete standalone docs after their fold tickets are complete.

Files to remove:
- docs/dev/GRAMMAR-old.md
- docs/dev/CTX-TO-MX-MIGRATION-PLAYBOOK.md
- docs/dev/PARALLEL-PIPELINE-PLAN.md
- docs/dev/INTERPRETER-CORE-EXTRACTION-MAP.md
- docs/dev/VARIABLE-IMPORTER-BOUNDARIES.md
- docs/dev/VARIABLE-IMPORTER-EXTRACTION-MAP.md
- docs/dev/CONTENT-LOADER-REFACTOR-SEAMS.md
- docs/dev/WITH.md
- docs/dev/EFFECTS.md
- docs/dev/STREAMING-ADAPTERS.md
- docs/dev/DEPENDENCIES.md
- docs/dev/DYNAMIC-MODULES.md
- docs/dev/TRANSFORMERS.md
- docs/dev/LARGE-VARIABLES.md
- docs/dev/LSP-TOKEN-VALIDATOR.md
- docs/dev/COMMAND-EXECUTION.md

## Acceptance Criteria

- All listed source files are removed.
- References in `related-docs` and cross-links are updated to remove deleted file paths.
- Remove operation runs only after fold tickets are complete.

**2026-02-18 00:37 UTC:** Verified prerequisites for removal are satisfied:
- All dependency fold tickets are closed: m-fac7, m-3d75, m-4a89, m-3d24, m-b453, m-953a, m-ea2a, m-aecf, m-8b23, m-e7fc, m-d3f3, m-fc23, m-4766, m-6feb, m-d002, m-e338.
- All listed obsolete source files still exist in `docs/dev/`.

Decisive removal list (all should be removed in this ticket)
- `docs/dev/GRAMMAR-old.md`
- `docs/dev/CTX-TO-MX-MIGRATION-PLAYBOOK.md`
- `docs/dev/PARALLEL-PIPELINE-PLAN.md`
- `docs/dev/INTERPRETER-CORE-EXTRACTION-MAP.md`
- `docs/dev/VARIABLE-IMPORTER-BOUNDARIES.md`
- `docs/dev/VARIABLE-IMPORTER-EXTRACTION-MAP.md`
- `docs/dev/CONTENT-LOADER-REFACTOR-SEAMS.md`
- `docs/dev/WITH.md`
- `docs/dev/EFFECTS.md`
- `docs/dev/STREAMING-ADAPTERS.md`
- `docs/dev/DEPENDENCIES.md`
- `docs/dev/DYNAMIC-MODULES.md`
- `docs/dev/TRANSFORMERS.md`
- `docs/dev/LARGE-VARIABLES.md`
- `docs/dev/LSP-TOKEN-VALIDATOR.md`
- `docs/dev/COMMAND-EXECUTION.md`

Required cross-link / related-doc cleanup before or with deletion
1) `docs/dev/ARCHITECTURE.md`
- Remove links to deleted docs:
  - `[EFFECTS.md](EFFECTS.md)`
  - `[DYNAMIC-MODULES.md](DYNAMIC-MODULES.md)`
  - `[TRANSFORMERS.md](TRANSFORMERS.md)`
- Replace each with the canonical targets already established by fold tickets:
  - effects -> `OUTPUT.md` / `STREAMING.md`
  - dynamic modules -> `SDK.md` / `RESOLVERS.md`
  - transformers -> `PIPELINE.md` / `DATA.md`

2) `docs/dev/SDK.md`
- In frontmatter `related-docs`, remove:
  - `docs/dev/EFFECTS.md`
  - `docs/dev/DYNAMIC-MODULES.md`

3) `docs/dev/STREAMING.md`
- In frontmatter `related-docs`, remove:
  - `docs/dev/EFFECTS.md`

Verification notes
- No other live docs/user-facing references to the 16 filenames were found outside the three files above.
- Remaining filename mentions are in audit artifacts under `runs/doc-audit-2026-02-17/...`; those are historical outputs and should NOT block docs cleanup.

Do NOT do
- Do not delete canonical replacement docs (`GRAMMAR.md`, `INTERPRETER.md`, `PIPELINE.md`, `DATA.md`, `OUTPUT.md`, `STREAMING.md`, `SDK.md`, `RESOLVERS.md`, `LANGUAGE-SERVER.md`, `MODULES.md`, etc.).
- Do not edit/remove audit artifact files under `runs/doc-audit-2026-02-17/` as part of this docs/dev cleanup ticket.
