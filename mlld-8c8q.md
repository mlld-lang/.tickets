---
id: mlld-8c8q
status: closed
deps: []
links: []
created: 2026-01-19T22:41:52.19636-08:00
type: task
priority: 2
---
# Path patterns in policy (glob syntax)

## Summary

Implement glob path patterns for policy configuration from spec v4.

## Usage in Policy

```mlld
policy {
  sources: {
    "src:file": {
      default: trusted,
      "/tmp/uploads/**": untrusted,
      "~/.config/**": trusted
    }
  },
  data: {
    "path:~/.ssh/**": sensitive,
    "path:~/.aws/**": sensitive,
    "path:@base/secrets/**": secret
  },
  capabilities: {
    filesystem: {
      read: ["**"],
      write: ["@base/tmp/**", "@base/dist/**"]
    }
  }
}
```

## Path Resolution

### Special Prefixes

| Prefix | Resolves To |
|--------|-------------|
| `@base` / `@root` | Location of mlld-config.json |
| `~` | User home directory |
| Absolute | Used as-is |
| Relative | Relative to @base |

### Resolution Logic

```typescript
function resolvePolicyPath(pattern: string, baseDir: string): string {
  if (pattern.startsWith("@base/") || pattern.startsWith("@root/")) {
    return path.join(baseDir, pattern.slice(6));
  }
  if (pattern.startsWith("~/")) {
    return path.join(os.homedir(), pattern.slice(2));
  }
  if (path.isAbsolute(pattern)) {
    return pattern;
  }
  return path.join(baseDir, pattern);
}
```

## Glob Library

Use `micromatch` (already a common dependency) or `picomatch`:

```typescript
import { isMatch } from "picomatch";

function matchesPattern(filePath: string, pattern: string, baseDir: string): boolean {
  const resolvedPattern = resolvePolicyPath(pattern, baseDir);
  const resolvedPath = path.resolve(filePath);
  return isMatch(resolvedPath, resolvedPattern, { 
    dot: true,
    matchBase: false 
  });
}
```

## Pattern Syntax

Standard glob patterns:
- `*` - Match any characters except `/`
- `**` - Match any characters including `/` (recursive)
- `?` - Match single character
- `[abc]` - Match character set
- `{a,b}` - Match alternatives

## Source Classification Matching

When file is read, check against source patterns:

```typescript
function classifyFileSource(
  filePath: string,
  sourceConfig: Record<string, unknown>,
  baseDir: string
): "trusted" | "untrusted" | undefined {
  // Check most specific patterns first (longer paths)
  const patterns = Object.keys(sourceConfig)
    .filter(k => k \!== "default")
    .sort((a, b) => b.length - a.length);
  
  for (const pattern of patterns) {
    if (matchesPattern(filePath, pattern, baseDir)) {
      return sourceConfig[pattern] as "trusted" | "untrusted";
    }
  }
  
  return sourceConfig.default as "trusted" | "untrusted" | undefined;
}
```

## Data Sensitivity Matching

When file is read, check against data patterns:

```typescript
function getFileSensitivity(
  filePath: string,
  dataConfig: Record<string, unknown>,
  baseDir: string
): string[] {
  const labels: string[] = [];
  
  for (const [pattern, label] of Object.entries(dataConfig)) {
    // Strip "path:" prefix if present
    const cleanPattern = pattern.startsWith("path:") 
      ? pattern.slice(5) 
      : pattern;
    
    if (matchesPattern(filePath, cleanPattern, baseDir)) {
      labels.push(label as string);
    }
  }
  
  return labels;
}
```

## Capability Filesystem Matching

```typescript
function canAccessPath(
  filePath: string,
  mode: "read" | "write",
  fsConfig: { read?: string[]; write?: string[] },
  baseDir: string
): boolean {
  const patterns = fsConfig[mode] ?? [];
  return patterns.some(p => matchesPattern(filePath, p, baseDir));
}
```

## Files to Modify

1. `core/policy/paths.ts` - New: path resolution and matching
2. `core/policy/union.ts` - Parse source/data patterns
3. `interpreter/eval/import/FileResolver.ts` - Apply source classification
4. `interpreter/policy/PolicyEnforcer.ts` - Check filesystem capabilities

## Dependencies

- `picomatch` or `micromatch` (glob matching)

## Exit Criteria

- [ ] `@base` / `@root` resolves to config location
- [ ] `~` resolves to home directory
- [ ] `**` glob patterns work
- [ ] Source patterns classify files as trusted/untrusted
- [ ] Data patterns add sensitivity labels
- [ ] Filesystem capability patterns enforced
- [ ] Most-specific pattern wins

## References

- spec-security-2026-v4.md Section 3.3

## Estimated Effort

~4 hours


