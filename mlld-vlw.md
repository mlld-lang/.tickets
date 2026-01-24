---
id: mlld-vlw
status: closed
deps: [mlld-tfb]
links: []
created: 2025-12-08T19:58:40.507851-08:00
type: task
priority: 2
parent: mlld-k4k
---
# Docs: update for strict mode

**Summary:**
Update all documentation to reflect the two-mode system.

**Files to update:**

1. **llms.txt:**
   - Add mode explanation at top
   - Note that examples use strict mode (no `/` prefix)
   - Reference `.mld.md` for prose-embedded scripts

2. **docs/user/ files:**
   - Add "Execution Modes" section to getting-started or new page
   - Update examples to show both styles where relevant
   - Clarify file extension semantics

3. **docs/dev/GRAMMAR.md:**
   - Document mode flag in parser options
   - Explain optional slash parsing
   - Document top-level line handling per mode

4. **README.md:**
   - Quick mention of file extensions and modes

**Key messaging:**

- `.mld` = code file, LLM-friendly, no implicit content
- `.mld.md` = documentation file, prose becomes output
- Optional `/` in strict mode for gradual migration
- SDK raw strings default to strict

**Example documentation:**

```markdown
## File Extensions

| Extension | Mode     | Behavior |
|-----------|----------|----------|
| `.mld`    | strict   | Every line is a directive or blank. Text lines error. |
| `.mld.md` | markdown | `/` required for directives. Text becomes content. |

### Strict Mode (.mld)
```mlld
var @name = "World"
show `Hello @name`
```

### Markdown Mode (.mld.md)
```mlld
# Welcome Script

This text becomes output.

/var @name = "World"
/show `Hello @name`
```
```

## Notes

Docs refactored for strict mode consistency:

- Removed slash prefixes from prose (e.g., /when → when) in reference.md, flow-control.md, introduction.md, modules.md, testing.md, mcp.md, security.md, registry.md, content-and-data.md
- Updated code examples to use primary template syntax (::@var::) instead of escape hatches (:::{{var}}:::)
- Created alternatives.md for escape hatch syntax (:::{{var}}:::, .mtt)
- Created markdown-mode.md for .mld.md file format
- Simplified introduction.md to focus on strict mode
- Simplified content-and-data.md template section with link to alternatives.md

Main docs now show ONE way (strict mode, .mld files, ::@var:: templates).


