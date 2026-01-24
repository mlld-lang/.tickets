---
id: mlld-5n7
status: closed
deps: [mlld-gj7]
links: []
created: 2025-12-08T19:57:44.878318-08:00
type: task
priority: 1
parent: mlld-k4k
---
# Interpreter: thread mode through parser invocation

**Summary:**
Wire the mode flag from entry points through to the parser, with extension-based defaults.

**Changes required:**

1. **Extension → mode mapping** (likely in `services/PathService.ts` or new utility):
   ```typescript
   function getModeFromPath(filepath: string): 'strict' | 'markdown' {
     if (filepath.endsWith('.mld.md') || filepath.endsWith('.md')) return 'markdown';
     if (filepath.endsWith('.mld')) return 'strict';
     return 'markdown'; // fallback for unknown
   }
   ```

2. **Entry points to update:**
   - `processMlld(script, options)` - add `mode` option, default `'strict'` for raw strings
   - `interpret(script, options)` - thread mode to parser
   - `execute(filepath, payload, options)` - derive mode from extension
   - CLI entry point - derive mode from input file extension

3. **Parser invocation** (wherever `parse()` is called):
   ```typescript
   const ast = parse(source, { 
     ...existingOptions,
     mode: options.mode ?? getModeFromPath(options.filePath) 
   });
   ```

4. **Options types** (`types/` or relevant interface files):
   - Add `mode?: 'strict' | 'markdown'` to ProcessOptions, InterpretOptions, ExecuteOptions

**Testing:**
- Unit test: `.mld` file parses in strict mode
- Unit test: `.mld.md` file parses in markdown mode
- Unit test: raw string defaults to strict
- Unit test: explicit mode option overrides extension inference


