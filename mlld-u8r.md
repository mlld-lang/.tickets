---
id: mlld-u8r
status: closed
deps: []
links: []
created: 2025-12-08T12:52:23.658146-08:00
type: task
priority: 2
assignee: codex
---
# Types: Extend RunValues/RunRaw/RunMeta for workingDir

## Context
Part of implementing cmd:path/sh:path feature. This task adds TypeScript type definitions for the new `workingDir` field in run directive AST nodes.

## Prerequisites
None - this can be done independently, but grammar tasks (mlld-b0e, mlld-zgb, mlld-rn5) will reference these types.

## Task
Extend the run directive type definitions to include working directory fields.

## Implementation

Location: `core/types/run.ts`

### 1. Extend RunValues interface

Find the `RunValues` interface and add:
```typescript
export interface RunValues {
  command?: ContentNodeArray;
  lang?: TextNodeArray;
  args?: VariableNodeArray;
  code?: ContentNodeArray;
  identifier?: VariableNodeArray;
  withClause?: WithClause;
  securityLabels?: DataLabel[];
  workingDir?: ContentNodeArray;  // NEW: Working directory path parts
}
```

### 2. Extend RunRaw interface

```typescript
export interface RunRaw {
  command?: string;
  lang?: string;
  args?: string[];
  code?: string;
  identifier?: string;
  withClause?: WithClause;
  securityLabels?: string;
  workingDir?: string;  // NEW: Raw working directory string
}
```

### 3. Extend RunMeta interface

```typescript
export interface RunMeta {
  isMultiLine?: boolean;
  argumentCount?: number;
  language?: string;
  hasVariables?: boolean;
  withClause?: WithClause;
  securityLabels?: DataLabel[];
  hasWorkingDir?: boolean;      // NEW: Flag indicating working dir is set
  workingDirMeta?: PathMeta;    // NEW: Path metadata for working dir
}
```

### 4. Check PathMeta import

Ensure `PathMeta` is imported if not already:
```typescript
import type { PathMeta } from './path'; // or wherever PathMeta is defined
```

## What These Fields Mean

- `workingDir` in RunValues: Array of AST nodes (Text nodes and VariableReference nodes) that make up the path
- `workingDir` in RunRaw: The raw string representation like "/tmp" or "@base/scripts"
- `hasWorkingDir` in RunMeta: Boolean flag to quickly check if workingDir was specified
- `workingDirMeta` in RunMeta: Metadata like `hasVariables: true`, `isAbsolute: true`, etc.

## Testing

After implementation:
```bash
npm run build
```

Should compile without errors. The grammar implementation will use these types.


