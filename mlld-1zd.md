---
id: mlld-1zd
status: closed
deps: [mlld-5n7]
links: []
created: 2025-12-08T20:56:43.129243-08:00
type: task
priority: 2
parent: mlld-k4k
---
# Analyzer: mode-aware parsing and cache keys

**Summary:**
Ensure `analyzeModule` respects file extension mode and caches correctly.

**Changes required:**

1. **Mode inference**:
   - `analyzeModule(filepath)` should infer mode from extension
   - Add optional `mode` parameter to override

2. **Parser call**:
   - Pass inferred/explicit mode to parser
   - Analyzer should parse `.mld` in strict mode, `.mld.md` in markdown mode

3. **Cache keys**:
   - Include mode in analyzer cache key (if analyzer has its own cache)
   - Or ensure it uses the shared AST cache which already includes mode (per mlld-ah5)

4. **Analysis results**:
   - No change to ModuleAnalysis structure needed
   - Mode affects parsing only, not analysis output

**Testing:**
- Analyze `.mld` file with bare directives - succeeds
- Analyze `.mld` file with text content - reports parse error
- Analyze `.mld.md` file with prose - succeeds


