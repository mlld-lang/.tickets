---
id: m-73be
status: closed
deps: []
created: 2026-02-22T17:21:28Z
type: bug
priority: 0
assignee: Adam Avenir
tags: [docs]
updated: 2026-02-22T18:48:46Z
---
# Document all output format specifiers (as json, as yaml, as text)

## What's wrong

The output directive docs (`docs/src/atoms/commands/output.md` or howto equivalent) don't show format specifiers.

## What to add

Document the `as <format>` syntax for the `output` directive with examples:

```mlld
output @data as json > results.json
output @data as yaml > config.yml
output @data as text > plain.txt
```

Show each specifier with a brief description of what it does (e.g., `as json` serializes objects/arrays to JSON string, `as text` converts to plain text representation). Check the interpreter source for the full list of supported format specifiers and document all of them.

