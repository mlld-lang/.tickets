---
id: mlld-5x5
status: closed
deps: [mlld-5n7]
links: []
created: 2025-12-08T20:56:33.43397-08:00
type: task
priority: 1
parent: mlld-k4k
---
# SDK/CLI: expose mode option and set defaults

**Summary:**
Expose `mode` option in SDK APIs and CLI, with sensible defaults.

**SDK changes:**

1. **processMlld(script, options)**:
   - Add `mode?: 'strict' | 'markdown'` to options
   - Default: `'strict'` when no `filePath` provided (raw strings)
   - Default: infer from extension when `filePath` provided

2. **interpret(script, options)**:
   - Add `mode` option, same defaults as processMlld

3. **execute(filepath, payload, options)**:
   - Add `mode` option
   - Default: infer from filepath extension
   - Explicit option overrides inference

4. **Type exports**:
   - Export `MlldMode = 'strict' | 'markdown'` type

**CLI changes:**

1. **Flag**: `--mode <strict|markdown>`
   - Overrides extension-based inference
   - Useful for testing or unusual file extensions

2. **Extension inference**:
   - `.mld` → strict
   - `.mld.md` / `.md` → markdown
   - stdin without `--mode` → strict (matches SDK raw string default)

3. **Help text**:
   - Document mode flag and extension defaults

**Testing:**
- SDK: raw string without filePath uses strict
- SDK: `.mld` file uses strict
- SDK: `.mld.md` file uses markdown
- SDK: explicit mode overrides extension
- CLI: `--mode markdown` on `.mld` file uses markdown
- CLI: stdin defaults to strict


