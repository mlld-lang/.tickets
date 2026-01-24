---
id: mlld-k3o
status: closed
deps: []
links: []
created: 2025-12-11T22:14:23.914668-08:00
type: task
priority: 0
---
# 4 locally passing tests failing  in CI

Investigating CI test failures:

1. **feat/builtin-exists** - FIXED: tmp/file.md was gitignored, renamed to base-tmp/

2. **gray-matter tests** - INVESTIGATING: Tests pass locally but fail in CI with 'Cannot find module gray-matter'. Added debug logging to NodeShadowEnvironment.ts to diagnose CI module resolution. The buildModulePaths() function now logs:
   - cwd
   - devNodeModules existence
   - Final module paths
   - mlldNodeModules location

Commit 23698c9a4 changed from require.resolve to createRequire(import.meta.url).resolve which may behave differently in CI.

Awaiting CI run to see debug output.


