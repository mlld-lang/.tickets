---
id: m-3a0a
status: closed
deps: []
created: 2026-02-25T19:48:44Z
type: chore
priority: 0
assignee: codex
updated: 2026-02-27T06:38:10Z
---
# Clean up stale Prettier references

Prettier was removed in Dec 2025 (3c3b54de7) but stale references remain: cli/index.ts:360 comment says 'Prettier v3 bug', cli/commands/test.ts:673 references non-existent PRETTIER-HANGING-ISSUE.md, help text mentions '--pretty' and '--no-format' as Prettier features, useMarkdownFormatter comments say 'prettier'. Keep the process.exit() calls (still needed for buffer flushing/clean exit) but update all comments and help text to reference the normalizer instead.

