---
id: mlld-ytz
status: closed
deps: [mlld-5n7]
links: []
created: 2025-12-08T20:56:53.299196-08:00
type: task
priority: 2
parent: mlld-k4k
---
# Dynamic modules: mode selection for injected content

**Summary:**
Ensure dynamically injected modules respect mode settings.

**Changes required:**

1. **String dynamic modules**:
   - `dynamicModules: { '@foo': 'var @x = 1' }` - needs to parse
   - Default: `'strict'` mode (programmatic injection = code, not docs)
   - Add option: `dynamicModuleMode?: 'strict' | 'markdown'`

2. **Object dynamic modules**:
   - `dynamicModules: { '@state': { count: 0 } }` - no parsing, N/A
   - These become direct value exports, mode irrelevant

3. **Per-module mode** (optional enhancement):
   - Could support: `dynamicModules: { '@foo': { source: '...', mode: 'markdown' } }`
   - Lower priority, probably not needed

4. **Documentation**:
   - Document that string dynamic modules parse in strict mode by default
   - Explain how to opt into markdown mode if needed

**Testing:**
- String dynamic module with bare directives - parses in strict
- String dynamic module with text content - errors (strict default)
- String dynamic module with explicit markdown mode - text becomes content


