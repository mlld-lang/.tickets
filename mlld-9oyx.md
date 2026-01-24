---
id: mlld-9oyx
status: open
deps: []
links: []
created: 2026-01-18T11:29:46.776675-08:00
type: task
priority: 2
parent: mlld-si08
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


