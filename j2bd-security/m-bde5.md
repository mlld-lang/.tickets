---
id: m-bde5
status: closed
deps: []
created: 2026-02-04T01:38:37Z
type: task
priority: 2
assignee: Adam
tags: [phase-1, doc]
updated: 2026-02-04T01:49:05Z
---
# Write mcp-import atom

Write the mcp-import atom documenting how to import MCP tools into mlld.

Location: docs/src/atoms/security/mcp-import.md

Key content:
- `import tools { @echo } from mcp "server"` (selected tools)
- `import tools from mcp "server" as @namespace` (namespace import)
- Tool names auto-convert between snake_case (MCP) and camelCase (mlld)
- Imported tools are callable like exe functions

Follow the atom format from existing atoms (see docs/src/atoms/security/env-overview.md for frontmatter pattern). Category: security. 100-200 words with working code examples that pass `mlld validate`.

Relevant code: grammar/directives/import.peggy (lines 117-245), interpreter/eval/import/ImportDirectiveEvaluator.ts, interpreter/mcp/McpImportManager.ts, core/mcp/names.ts


**2026-02-04 01:49 UTC:** Worker failed 3x with 'no output' (prompt was 127K chars). Decision agent wrote the atom directly using patterns from existing atoms and test cases.
