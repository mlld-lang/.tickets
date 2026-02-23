---
id: m-1bb8
status: closed
deps: []
created: 2026-02-22T17:20:42Z
type: feature
priority: 0
assignee: Adam Avenir
tags: [imports, modules]
updated: 2026-02-22T18:48:46Z
---
# Implement directory module imports — import dir resolves to dir/index.mld

## Current behavior

`import from './mymodule'` where `mymodule` is a directory always does a collection import — scans `mymodule/*/index.mld` to build a namespace of sub-modules. There is no way to import `mymodule/index.mld` as just `mymodule`.

## New convention

- **No trailing slash** = directory module import: `import { @fn } from './mymodule'` resolves to `./mymodule/index.mld`
- **Trailing slash** = collection import: `import './agents/' as @agents` scans `./agents/*/index.mld` (current behavior)

## Implementation

In `interpreter/eval/import/DirectoryImportHandler.ts`:
1. When the import path has no trailing slash and points to a directory, first check if `dir/index.mld` exists. If so, import that file directly (not as a collection).
2. When the import path has a trailing slash, use current collection import behavior (scan subdirectories for `*/index.mld`).
3. If no trailing slash and no `index.mld` exists in the directory, fall back to collection import for backward compatibility (and consider emitting a warning suggesting trailing slash).

