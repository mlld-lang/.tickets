---
id: mlld-9oyx
status: closed
deps: []
links: []
created: 2026-01-18T11:29:46.776675-08:00
type: task
priority: 2
tags: [size-s, complexity-s, risk-s, impl-none]
parent: mlld-si08
updated: 2026-01-31T09:47:28Z
---
# Capture skills and support local vs global config selection

## Summary

Improve `mlld env capture` to:

1. **Copy skills directory** - Currently only copies settings.json, CLAUDE.md, hooks.json
2. **Support local vs global selection** - Ask which config to capture: local `.claude/`, global `~/.claude/`, or both

## Current State

```typescript
const filesToCopy = ["settings.json", "CLAUDE.md", "hooks.json"];
```

Missing:
- `skills/` directory (slash commands and custom skills)
- Local project `.claude/` config option

## Proposed Changes

### 1. Copy skills directory
```typescript
const filesToCopy = ["settings.json", "CLAUDE.md", "hooks.json"];
const dirsToCopy = ["skills"];  // NEW
```

### 2. Config source selection

When running `mlld env capture`:
- Detect if local `.claude/` exists in cwd
- Detect if global `~/.claude/` exists
- If both: prompt user which to capture (or both merged)
- Flag: `--local`, `--global`, `--both`

### 3. Same for Codex

Support `.codex/` and `~/.codex/` following same pattern.

## Exit Criteria

- [ ] `skills/` directory captured
- [ ] Local vs global config selection works
- [ ] Codex support (`.codex/`)

## References

- cli/commands/env.ts:538 (filesToCopy list)
- mlld-env-proposal.md



**2026-01-31 09:47 UTC:** Implemented all features:
- skills/ directory is now copied during capture
- --local flag captures from project .claude/ instead of ~/.claude/
- --codex flag captures from .codex/ config (global by default, --local for project)
- --global flag stores environment in ~/.mlld/env/ instead of project .mlld/env/
- Updated help text to document new options
- Added tests for all new functionality
- Build passes, all tests pass
