---
id: m-b4c6
status: open
deps: []
created: 2026-02-17T15:13:22Z
type: task
priority: 2
assignee: Adam
tags: [plugin, docs-writer, dx]
updated: 2026-02-17T15:13:26Z
---
# Fold plugin skill docs into docs-writer staleness prevention

## Problem

Plugin skill docs (plugins/mlld/skills/orchestrator/, plugins/mlld/skills/agents/) can drift out of sync with the actual mlld runtime as we make updates. We already have a docs-writer process for the howto atoms (docs/src/atoms/). The plugin skills reference mlld syntax patterns, built-in pipes, methods, and conventions that can become stale.

Example: the orchestrator skill referenced `@fileExists()` as if it were a builtin, used `@json` pipe instead of the current `@parse`, and pointed to nonexistent example directories.

## Solution

Extend the docs-writer process to also cover plugin skill docs. When mlld syntax, builtins, or conventions change, the docs-writer should check and update:

- `plugins/mlld/skills/orchestrator/SKILL.md`
- `plugins/mlld/skills/orchestrator/syntax-reference.md`
- `plugins/mlld/skills/orchestrator/gotchas.md`
- `plugins/mlld/skills/agents/SKILL.md`
- Any other plugin skill docs that reference mlld syntax

The docs-writer already knows how to verify code examples against the runtime. Apply the same approach to plugin skill code examples.

## Acceptance criteria

- [ ] Plugin skill docs are included in the docs-writer's scope
- [ ] Code examples in skill docs are validated against current mlld syntax
- [ ] Stale references (deprecated pipes, non-existent builtins, wrong paths) are caught and updated

