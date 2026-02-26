---
id: m-69dc
status: open
deps: [m-5870, m-3f0b, m-fd36, m-a3fe, m-9bab, m-5c21]
created: 2026-02-20T01:13:20Z
type: task
priority: 3
assignee: Adam Avenir
updated: 2026-02-23T23:37:50Z
links: 
---
# Update skills and examples in plugins/ to match orchestrator conventions

## Context

After shared libraries (m-5870, m-3f0b, m-fd36, m-a3fe), output migration (m-9bab), and review generalization (m-5c21) are complete, the skills and examples in `plugins/` need updating to match.

## Source files to update

Check all files in:
- `plugins/mlld/skills/orchestrator/` — the orchestrator scaffold skill
- `plugins/mlld/skills/` — any other skills that generate or reference orchestrator code
- Any example `.mld` files in `plugins/` that show orchestrator patterns

## Required changes

1. **Scaffold skill** (`plugins/mlld/skills/orchestrator/`): Update generated scaffolds to use:
   - `import { @mkdirp, @fileExists } from @lib/common`
   - `import { @logEvent, @logPhaseStart, @logPhaseComplete } from @lib/events`
   - `import { @resolveOutputDir } from @lib/runs`
   - `@output/` for run output (not custom `@base/` paths)
   - Config-driven pattern from CONVENTIONS.md where applicable

2. **Example code in SKILL.md files**: Update any inline examples that show:
   - Local `exe @mkdirp` definitions → import from `@lib/common`
   - Custom output paths → `@resolveOutputDir`
   - Inline event logging → import from `@lib/events`

3. **Remove stale patterns**: Any examples that show the old output conventions (custom `runs/` paths, `@base/pipeline/`, etc.) should be updated.

## Acceptance criteria

- Scaffold skill generates code that uses `@lib/` imports.
- No skill or example shows locally-defined `@mkdirp`, `@fileExists`, or `@flatName`.
- Example output paths use `@output/` convention.
- All skills pass `mlld validate` (if applicable).

