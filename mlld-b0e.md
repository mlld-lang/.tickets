---
id: mlld-b0e
status: closed
deps: []
links: []
created: 2025-12-08T12:52:06.303866-08:00
type: task
priority: 2
assignee: codex
---
# Grammar: Add WorkingDirPath pattern for :path suffix parsing

## Context
Part of implementing cmd:path/sh:path feature to allow setting working directory for command execution.

Example syntax:
```mlld
/run cmd:/Users/adam/dev/mlld {mlld setup}
/run sh:@mypath {echo "hello world"}
/run cmd:@base/scripts {./build.sh}
/run sh:/Users/@username/dev/@dir {somecommand}
```

## Task
Create a new grammar pattern `WorkingDirPath` to parse the `:path` suffix that appears after `cmd` or language keywords (`sh`, `bash`, etc).

## Requirements

1. Path must be rooted (absolute) - starts with `/` or `@`
2. Support variable interpolation: `@mypath`, `@base/subdir`, `/home/@user/dev`
3. No relative paths allowed (no `./` or bare names)

## Implementation

Location: `grammar/patterns/unified-run-content.peggy`

Add these patterns:

```peggy
// Working directory path suffix for cmd and sh
WorkingDirPath "working directory path"
  = ":" path:WorkingDirPathContent {
      return path;
    }

// Path content with variable interpolation
WorkingDirPathContent
  = parts:WorkingDirPathPart+ {
      const flatParts = parts.flat();
      const raw = helpers.reconstructRawString(flatParts);
      
      return {
        type: 'workingDir',
        parts: flatParts,
        raw: raw,
        meta: helpers.createPathMetadata(raw, flatParts)
      };
    }

// Individual path parts - variables or literal path segments
WorkingDirPathPart
  = UnifiedVariableNoTail       // @mypath or @base etc.
  / WorkingDirPathLiteral       // /path/segments

// Literal path segments (no whitespace, not braces)
WorkingDirPathLiteral
  = chars:WorkingDirPathChar+ {
      return helpers.createNode(NodeType.Text, { 
        content: chars.join(''), 
        location: location() 
      });
    }

WorkingDirPathChar
  = [^\s\t\n\r\{\}@]  // Any char except whitespace, braces, @
```

## Key Points

- Reuse `UnifiedVariableNoTail` for variable parsing (already exists)
- Return structure with `parts` (AST nodes) and `raw` (string)
- Use `helpers.createPathMetadata()` to create metadata
- Must stop at whitespace or `{` (command block start)

## Testing

After implementation, test with `npm run ast`:
```bash
npm run ast -- 'cmd:/absolute/path {ls}'
npm run ast -- 'cmd:@variable {ls}'
npm run ast -- 'cmd:@base/subdir {ls}'
```

## Notes

Grammar pattern should parse :path suffix for cmd/sh etc; path content allows absolute Unix paths only; no Windows or '~'; '/' valid.


