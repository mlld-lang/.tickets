---
id: mlld-ah5
status: closed
deps: [mlld-5n7]
links: []
created: 2025-12-08T19:57:52.664361-08:00
type: task
priority: 2
parent: mlld-k4k
---
# Cache: include mode in AST cache keys

**Summary:**
Ensure AST cache doesn't return wrong-mode cached AST.

**Changes required:**

1. **Locate AST cache** (likely in interpreter or execute flow):
   - Find where parsed ASTs are cached by filepath

2. **Update cache key**:
   ```typescript
   // Before
   const cacheKey = filepath;
   
   // After
   const cacheKey = `${filepath}:${mode}`;
   ```
   
   Or use a composite key object if the cache supports it.

3. **Cache invalidation**:
   - Same file parsed with different mode = different cache entries
   - mtime-based invalidation still applies per entry

**Testing:**
- Parse same file in strict mode, cache hit
- Parse same file in markdown mode, cache miss (different key)
- Modify file, both cache entries invalidate


