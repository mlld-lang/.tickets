---
id: mlld-wsm
status: closed
deps: [mlld-5n7]
links: []
created: 2025-12-08T19:58:02.459003-08:00
type: task
priority: 2
parent: mlld-k4k
---
# Resolvers: propagate mode through imports

**Summary:**
When resolving imports, ensure the imported module is parsed with the correct mode based on its extension.

**Changes required:**

1. **Import resolution** (resolvers, likely `LocalResolver`, `RegistryResolver`, etc.):
   - When resolving a module path, determine mode from resolved filepath extension
   - Pass mode to parser when loading the imported module

2. **Mode inheritance rules:**
   - `.mld` imports → strict mode
   - `.mld.md` imports → markdown mode
   - The importing file's mode does NOT affect the imported file's mode
   - Each file's extension determines its own mode

3. **Dynamic modules:**
   - String dynamic modules: default to `'strict'` (or accept mode option)
   - Object dynamic modules: N/A (no parsing)

4. **Edge cases:**
   - Circular imports: mode still derived from extension
   - Re-exports: mode of original source file applies

**Testing:**
- `.mld` file imports `.mld.md` file - each parses in correct mode
- `.mld.md` file imports `.mld` file - each parses in correct mode
- Dynamic module string defaults to strict


