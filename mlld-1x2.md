---
id: mlld-1x2
status: closed
deps: []
links: []
created: 2025-12-08T12:52:30.564083-08:00
type: task
priority: 2
assignee: codex
---
# Interpreter: Create cwd-resolver utility for path validation

## Context
Part of implementing cmd:path/sh:path feature. This task creates a utility function to resolve and validate working directory paths before command execution.

## Prerequisites
- Grammar tasks complete (workingDir is in AST)
- Type tasks complete (workingDir types defined)

## Task
Create `interpreter/utils/cwd-resolver.ts` to handle path interpolation and validation.

## Requirements

The resolver must:
1. Interpolate variables in the path (`@mypath`, `/home/@user/dev`)
2. Validate path is rooted (starts with `/`)
3. Check directory exists
4. Check path is actually a directory (not a file)
5. Return absolute path or throw clear errors

## Implementation

Create new file: `interpreter/utils/cwd-resolver.ts`

```typescript
import * as fs from 'fs';
import type { ContentNodeArray } from '@core/types';
import type { Environment } from '../env/Environment';
import { interpolate, InterpolationContext } from '../core/interpreter';
import { MlldCommandExecutionError } from '@core/errors';

export async function resolveCwd(
  workingDirNodes: ContentNodeArray,
  env: Environment,
  sourceLocation?: any
): Promise<string> {
  // Step 1: Interpolate variables
  const interpolatedPath = await interpolate(
    workingDirNodes,
    env,
    undefined,
    { context: InterpolationContext.FilePath }
  );
  
  // Step 2: Validate rooted (absolute) path
  if (!interpolatedPath.startsWith('/')) {
    throw new MlldCommandExecutionError(
      `Working directory must be an absolute path (start with /): ${interpolatedPath}`,
      { location: sourceLocation }
    );
  }
  
  // Step 3: Check directory exists
  if (!fs.existsSync(interpolatedPath)) {
    throw new MlldCommandExecutionError(
      `Working directory does not exist: ${interpolatedPath}`,
      { location: sourceLocation }
    );
  }
  
  // Step 4: Check is directory
  const stats = fs.statSync(interpolatedPath);
  if (!stats.isDirectory()) {
    throw new MlldCommandExecutionError(
      `Working directory path is not a directory: ${interpolatedPath}`,
      { location: sourceLocation }
    );
  }
  
  return interpolatedPath;
}
```

## Error Messages

Provide clear errors for common issues:
- Undefined variable: "Cannot resolve working directory: variable @username is not defined"
- Not rooted: "Working directory must be an absolute path (start with /): ./relative"
- Doesn't exist: "Working directory does not exist: /nonexistent/path"
- Not a directory: "Working directory path is not a directory: /etc/hosts"

## Testing

Create unit tests in `interpreter/utils/cwd-resolver.test.ts`:
```typescript
describe('resolveCwd', () => {
  it('should resolve absolute path', async () => {
    // Test with /tmp
  });
  
  it('should interpolate variables', async () => {
    // Test with @mypath variable
  });
  
  it('should error on non-rooted path', async () => {
    // Test ./relative fails
  });
  
  it('should error on non-existent path', async () => {
    // Test /does/not/exist fails
  });
  
  it('should error on file path', async () => {
    // Test /etc/hosts fails (is file not dir)
  });
});
```

## Notes

Validation rules: absolute Unix paths only (/ ok); no Windows or '~'; reuse existing path error pattern on missing/non-dir; supports variable interpolation before validation.


