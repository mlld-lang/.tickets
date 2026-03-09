---
id: m-d4e5
status: closed
deps: [m-86f5, m-3bea]
links: []
created: 2026-03-04T01:40:08Z
type: epic
priority: 2
assignee: Adam
tags: [phase-9, metadata, audit]
updated: 2026-03-04T04:54:51Z
---
# Phase 9: .mx.edits/.mx.diff + audit chain

Spec: `_specs/box.md` (Implementation order, Phase 9)

Metadata extensions for workspace inspection. Workspace-level diff. Taint reconstruction from audit log.

## Features

### .mx.edits — query workspace changes
```mlld
box @workspace [
  file "task.md" = @task
  run cmd { echo "done" >> task.md }
]
show @workspace.mx.edits    >> list of changed files with change types
```

Returns the result of `VirtualFS.changes()` — an array of `VirtualFSChange` objects:
```typescript
interface VirtualFSChange {
  path: string;
  type: 'created' | 'modified' | 'deleted';
  entity: 'file' | 'directory';
}
```

### .mx.diff — unified diff output
```mlld
show @workspace.mx.diff     >> unified diff of all workspace changes
show @file.mx.diff           >> diff for a single file
```

Workspace-level: calls `VirtualFS.diff()` (alias for `changes()`).
Per-file: calls `VirtualFS.fileDiff(path)` which returns a unified diff string.

### Audit chain
Track provenance of workspace modifications:
- Each write to VFS (via `file`/`files` directives or ShellSession commands) gets an audit log entry
- Audit entries record: source directive/command, timestamp, path, change type
- Taint labels from source data propagate to written files
- `.mx.sources` on workspace files reflects the chain of writes

## Implementation

### Metadata resolution
Extend the `.mx.*` metadata resolution (wherever it currently lives in the evaluator) to handle WorkspaceValue:
- When value is WorkspaceValue and `.mx.edits` is accessed → call `workspace.fs.changes()`
- When value is WorkspaceValue and `.mx.diff` is accessed → call `workspace.fs.diff()` or aggregate `fileDiff()` results
- Per-file `.mx.diff` works when the value is a file read from a workspace resolver

### Audit log entries
When writes occur to a workspace VFS:
- Record the write operation in the existing audit log infrastructure
- Include the source (which directive/command triggered the write)
- Include taint labels carried by the written content

### Workspace result availability
After a box block executes, the workspace value must remain accessible for inspection:
```mlld
var @result = box @workspace [
  file "task.md" = @task
  run cmd { echo "done" >> task.md }
]
show @result.mx.edits
show @result.mx.diff
```

## Acceptance Criteria
- `.mx.edits` returns correct change list for workspace (created, modified, deleted)
- `.mx.diff` returns unified diff (workspace-level)
- Per-file `.mx.diff` returns diff for individual files
- Audit log captures workspace write provenance (source directive, timestamp, path)
- Metadata resolution handles WorkspaceValue type correctly
- Tests for `.mx.edits` with various change types
- Tests for `.mx.diff` output format
- Tests for audit log entries on workspace writes
- Docs atoms for workspace inspection metadata
- All existing tests pass
- Committed before proceeding to Phase 10
