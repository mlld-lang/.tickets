---
id: mlld-3ma
status: open
deps: [mlld-tfb]
links: []
created: 2025-12-08T20:57:14.29758-08:00
type: task
priority: 3
parent: mlld-k4k
---
# Migration: lint warning for prose in .mld files

**Summary:**
Help users migrate by warning when `.mld` files contain prose that will error in strict mode.

**Changes required:**

1. **Lint rule / diagnostic**:
   - Scan `.mld` files for lines that would be text content
   - Emit warning: "This file contains prose on line X. Rename to .mld.md or remove text."

2. **CLI flag**:
   - `mlld lint --check-mode` or similar
   - Could be part of `mlld check` if that exists

3. **Error message enhancement**:
   - When strict mode parser errors on text, include helpful message:
   - "Text content not allowed in .mld files. Either:\n  1. Rename to .mld.md to embed prose\n  2. Remove or comment out text lines"

4. **Migration script** (optional):
   - `mlld migrate --to-strict` - rename files and report issues
   - Low priority, users can do this manually

**Testing:**
- Lint `.mld` file with prose - warns with line numbers
- Lint clean `.mld` file - no warnings
- Error message on strict parse failure includes migration hint


