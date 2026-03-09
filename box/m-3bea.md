---
id: m-3bea
status: closed
deps: [m-04c2]
links: []
created: 2026-03-04T01:38:40Z
type: epic
priority: 1
assignee: Adam
tags: [phase-4, file-directives, grammar]
updated: 2026-03-04T03:52:47Z
---
# Phase 4: file/files directives

Spec: `_specs/box.md` (Part 5: file/files inside box blocks)

Grammar, AST, evaluator for `file` and `files` directives. Named and anonymous forms. Resolver creation on first use. Path validation. Immutability enforcement.

## Syntax

### file directive (single file projection)
```mlld
file "task.md" = @content
file "src/index.js" = @source
```
Writes a single file. Inside a box block, writes to the active workspace VFS. Outside a box, creates a standalone file on the real filesystem.

### files directive — named resolver form
```mlld
files <@workspace/> = [
  { "index.js": @source, desc: "Entry point" },
  { "config.json": @config }
]

files <@workspace/src/> = [
  { "main.ts": @code }
]
```
Creates or extends a VFS-backed resolver. The `<@resolver/>` syntax indicates a named resolver target. First use creates a `VirtualFS.empty()` and registers it as a resolver. Subsequent `files <@resolver/subpath>` calls write to the same VFS.

### files directive — anonymous form (inside box blocks)
```mlld
box [
  file "task.md" = @task
  files "src/" = [
    { "index.js": @source, desc: "Entry point" }
  ]
  exe @agent-handler
]
```
When `file`/`files` appear inside box blocks without a `<@resolver/>` target, they write to the box's workspace VFS. No resolver is created — files are scoped to the box.

### Description metadata
The `desc:` field on file entries is captured as metadata on the workspace:
```typescript
workspace.descriptions.set(path, description);
```
This enables agents to understand file purposes without reading content.

## Grammar Changes

### New directive rules
- `file` keyword + string path + `=` + expression
- `files` keyword + (resolver target OR string path) + `=` + array expression
- Array entries: objects with string key (filename) → expression value, optional `desc:` field

### Block statement grammar
Add `file` and `files` to `ExeStatementBlock` in `grammar/patterns/exe-rhs.peggy`. Currently the block grammar only allows `let`, `when`, `for`, etc. — `file`/`files` must be added to the valid statement list so they parse inside box/exe blocks.

## AST Types (core/types/)

```typescript
interface FileDirectiveNode extends TypedDirectiveNode<'file', 'file'> {
  values: {
    path: string;          // target file path
    content: BaseMlldNode[]; // content expression
    resolverTarget?: string; // @resolver name if <@resolver/> form
  };
}

interface FilesDirectiveNode extends TypedDirectiveNode<'files', 'files'> {
  values: {
    path: string;          // base path or resolver target
    entries: FileEntry[];   // file entries with optional descriptions
    resolverTarget?: string;
  };
}

interface FileEntry {
  name: string;
  content: BaseMlldNode[];
  description?: string;
}
```

## Evaluator

### Resolver creation on first use
When `files <@workspace/>` is used and `@workspace` doesn't exist yet:
1. Create `VirtualFS.empty()`
2. Register as a resolver variable: `env.setVariable('@workspace', workspaceValue)`
3. Write files to the VFS

When `@workspace` already exists (from a previous `files` call), extend it by writing additional files to the same VFS.

### Immutability enforcement
Once a file is written to a VFS, it cannot be overwritten by another `file`/`files` call in the same scope. Attempting to write to an already-existing path throws `MlldDirectiveError`. Commands inside box blocks CAN modify files (they go through ShellSession), but directive-level writes are one-shot.

### Write routing
All writes go through the Phase 3 unified write abstraction (`executeWrite()`):
- Inside box block with active workspace → VFS write
- Named resolver target (`<@resolver/>`) → resolver VFS write
- Outside box, no resolver target → real filesystem write

### Path validation
- Reject absolute paths (files are always relative to workspace root or resolver base)
- Reject `..` path traversal
- Normalize path separators

## Acceptance Criteria
- `file` directive parses and evaluates (standalone and in blocks)
- `files` directive parses and evaluates (named resolver and anonymous forms)
- Resolver creation works (`files <@workspace/>` creates VFS-backed resolver on first use)
- Subsequent `files <@workspace/subdir>` writes to same VFS
- Path validation rejects absolute paths and `..` traversal
- Immutability enforced (duplicate path throws error)
- `file`/`files` work inside box blocks (write to workspace VFS)
- `file`/`files` work outside box blocks (create resolvers or write to filesystem)
- Description metadata captured in `workspace.descriptions`
- Grammar tests for all forms (standalone, block, resolver target, anonymous)
- Evaluator tests for all forms
- Fixture tests for standalone and block contexts
- Docs: new atoms in `docs/src/atoms/` for file and files directives
- All existing tests pass
- Committed before proceeding to Phase 5
