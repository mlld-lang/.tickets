---
id: mlld-e3g2
status: closed
deps: [mlld-rtx0, mlld-76rt, mlld-c80y, mlld-spaf, mlld-ifha, mlld-beql]
links: []
created: 2026-01-05T15:15:57.133661-08:00
type: epic
priority: 1
---
# Atom-based documentation system with mlld howto CLI

Build componentized documentation using atomic markdown files that can be assembled into different outputs (llm docs, CLI help, website). Includes mlld howto command for self-documenting help.

Design thread: mlld-e3g2-design (thrd-929hew4t)
Pattern docs: docs/dev/HOWTO-PATTERN.md

## Completed (Vertical Slice)
- ✅ Created docs/src/atoms/control-flow/ with 20 atoms
- ✅ Built mlld howto CLI command (tree/topic/subtopic views)
- ✅ Verified atom assembly (zero-diff rebuild)
- ✅ Added mlld qs quickstart command
- ✅ Documented pattern for adoption by other tools

## Remaining Work
See dependencies for tasks covering all atom categories.


