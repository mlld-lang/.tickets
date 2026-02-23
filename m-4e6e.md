---
id: m-4e6e
status: open
deps: []
created: 2026-02-23T16:39:50Z
type: bug
priority: 0
assignee: Adam Avenir
tags: [grammar, escaping, cmd]
updated: 2026-02-23T16:39:53Z
---
# Audit and fix all @ escaping — \@ should produce literal @ everywhere

## Current behavior

`\@` escaping is position-sensitive in cmd blocks — it works at line start or after an @variable but fails after literal text. The template parser handles `\@` correctly in all positions. `@@` works in templates/strings to produce literal `@` but behavior is inconsistent across contexts.

## Expected behavior

- `\@` should produce a literal `@` character in ALL contexts: cmd blocks, templates, strings, everywhere. No position sensitivity.
- `@@` should produce a literal `@` in templates and strings.

## Fix

Audit all @ escaping across the cmd tokenizer, template parser, and string interpolation. The template parser is the reference implementation — make cmd block escaping match it. Add regression tests for \@ after literal text in cmd blocks specifically.

