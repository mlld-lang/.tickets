---
id: mlld-xrsy
status: closed
deps: []
links: []
created: 2026-01-20T16:30:57.061971-08:00
type: epic
priority: 3
parent: mlld-awwp
---
# Python Package Imports

Import Python packages (pip/uv) into mlld scripts analogous to Node.js module handling.

**Key Design Decisions:**
1. Extend mlld-lock.json (not separate file) for Python package tracking
2. Track venv config in mlld-config.json 
3. Support both pip and uv with auto-detection (prefer uv)
4. Import type: `py` (with `python` alias)

**Reference:** Node approach uses node-interop.ts, ModuleInstaller, content-addressed cache


