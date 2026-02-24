---
id: m-a17b
status: closed
deps: []
links: []
created: 2026-02-23T20:23:46Z
type: bug
priority: 0
assignee: Adam
tags: [grammar, parser, node-blocks]
updated: 2026-02-24T13:35:11Z
---
# Regex with character classes in node/js blocks causes cascading parse failure in full-file context

## Summary

A JavaScript regex containing character classes with quotes (`/^["']|["']$/g`) inside a `node {}` block parses correctly in isolation but causes cascading parse failures when the full file is parsed. The grammar appears to consume or mistrack state from the regex content, breaking parsing of subsequent directives.

## Reproduction

The file `llm/run/docs-audit/index.mld` fails to parse. The specific line causing the issue is line 49:

```javascript
return line ? line.slice(key.length + 1).trim().replace(/^["']|["']$/g, '') : '';
```

### What works:
- The `node {}` block containing this line parses correctly **in isolation**
- The full file parses correctly when this line is **simplified** (remove the regex)
- A simple regex like `/test/g` in the same position works fine

### What fails:
- The full file fails with "Invalid exe syntax" error pointing at line 42 (the `exe` declaration), not the actual problematic line 49
- The LSP tokenizer (which uses the grammar) breaks syntax highlighting for the entire remainder of the file

### Investigation steps:
1. `mlld validate` reports "Invalid exe syntax" at line 42 — misleading, since line 42 is the `exe @extractMeta(atom) = node {` declaration which is valid
2. Binary search confirmed: `head -250` of the file parses, `head -260` does not
3. Isolating the `node {}` block alone: parses fine
4. Replacing the regex with a simple one (`/test/g`): full file parses
5. The regex `/^["']|["']$/g` appears to be the trigger — likely the combination of `["']` character classes with pipe `|` and/or the quotes inside the character class

### Likely cause:
The grammar's `node {}` / `js {}` block content parser is not fully opaque to the content — something in the regex (character classes with quotes, or the `|` operator) is being partially interpreted by the grammar, corrupting parser state that manifests as failures much later in the file.

### Error reporting issue:
The error message points to line 42 (the `exe` declaration) rather than line 49 (the actual problematic content). This makes debugging extremely difficult. `mlld validate` should either:
- Report the actual location within the code block where parsing goes wrong
- Or at minimum, report the correct line number

## Affected file

`llm/run/docs-audit/index.mld` — full contents at time of filing are the current version on branch rc83.

## Expected behavior

1. `node {}` and `js {}` blocks should treat their content as opaque — the grammar should only scan for balanced braces to find the block boundary, not interpret the content
2. If parsing does fail inside a code block, the error should point to the actual location


## Notes

**2026-02-23T20:24:38Z** Original failing file preserved at tmp/docs-audit-index.mld for reproduction.
