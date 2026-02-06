---
id: mlld-4zy
status: open
deps: [mlld-gj7]
links: []
created: 2025-12-08T20:57:03.568773-08:00
type: task
priority: 3
tags: [size-m, complexity-m, risk-m, impl-none]
parent: mlld-k4k
---
# Formatter: mode-aware slash prefix handling

**Summary:**
Formatter should emit appropriate slash prefix based on mode.

**Changes required:**

1. **Mode detection**:
   - Formatter receives mode from file extension or explicit option
   - Track mode throughout formatting pass

2. **Directive formatting**:
   - Markdown mode: always emit `/` prefix on directives
   - Strict mode: configurable behavior:
     - Option A: emit bare directives (no `/`)
     - Option B: preserve input style (if had `/`, keep it)
     - Option C: always emit `/` for compatibility
   - Recommend Option A as default, Option B for `--preserve-style`

3. **Blank line handling**:
   - Strict mode: blank lines are formatting whitespace, preserve for readability
   - Markdown mode: blank lines may be content, preserve carefully

4. **Config option**:
   - `strictModeSlash: 'omit' | 'preserve' | 'include'`
   - Or simpler: `--bare-directives` flag for strict mode

**Testing:**
- Format `.mld` file - outputs bare directives (if Option A)
- Format `.mld.md` file - outputs `/` prefixed directives
- Format `.mld` with mixed slash usage - normalizes per config


