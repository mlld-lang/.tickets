---
id: mlld-rn5
status: closed
deps: []
links: []
created: 2025-12-08T12:52:18.829051-08:00
type: task
priority: 2
assignee: codex
---
# Grammar: Extend RunLanguageCodeCore to support :path for sh/bash

## Context
Part of implementing cmd:path/sh:path feature. This task extends language code execution (`sh`, `bash`, `js`, `python`, etc.) to support the `:path` suffix.

## Prerequisites
- mlld-b0e must be complete (WorkingDirPath pattern exists)

## Task
Modify `RunLanguageCodeCore` to optionally parse and include working directory path for language executors.

## Implementation

Location: `grammar/core/code.peggy`

### Step 1: Add language with path pattern

```peggy
// Shared working directory pattern for language codes
LanguageWorkingDirPath "language working directory path"
  = ":" path:WorkingDirPathContent {
      return path;
    }

// Run language with optional working directory
RunCodeLanguageWithPath
  = language:RunCodeLanguage workingDir:LanguageWorkingDirPath? {
      return {
        language: language,
        workingDir: workingDir || null
      };
    }
```

### Step 2: Modify RunLanguageCodeCore

Find `RunLanguageCodeCore` pattern (around line 208) and update it to:
1. Use `RunCodeLanguageWithPath` instead of `RunCodeLanguage`
2. Extract `workingDir` from `langInfo`
3. Add workingDir to values/raw/meta when present

```peggy
RunLanguageCodeCore
  = streamPrefix:StreamKeyword? _ langInfo:RunCodeLanguageWithPath _ code:UnifiedCodeBrackets {
      const language = langInfo.language;
      const workingDir = langInfo.workingDir;
      
      // ... existing code to process language and code ...
      
      const values = {
        lang: [langNode],
        args: [],
        code: [codeNode]
      };
      
      if (workingDir) {
        values.workingDir = workingDir.parts;
      }
      
      const raw = {
        lang: language,
        args: [],
        code: codeContent
      };
      
      if (workingDir) {
        raw.workingDir = workingDir.raw;
      }
      
      const meta = {
        isMultiLine: code.isMultiLine || codeContent.includes('\n'),
        language: language,
        hasVariables: false,
        hasWorkingDir: \!\!workingDir
      };
      
      if (workingDir) {
        meta.workingDirMeta = workingDir.meta;
      }
      
      // ... rest of existing code ...
    }
```

## Examples

```mlld
/run sh:/tmp {echo "hello"}
/run bash:@mypath {pwd}
/run js:/home/@user/scripts {console.log(process.cwd())}
```

## Testing

```bash
npm run ast -- '/run sh:/tmp {echo hello}'
npm run ast -- '/run bash:@base {pwd}'
npm run ast -- '/run python:/scripts {print(1)}'
```

## Notes

RunLanguageCodeCore needs :path using WorkingDirPath; absolute Unix paths only ('/' ok); no Windows or '~'.


