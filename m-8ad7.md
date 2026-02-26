---
id: m-8ad7
status: closed
deps: []
created: 2026-02-24T20:46:02Z
type: chore
priority: 0
assignee: Adam Avenir
updated: 2026-02-25T00:57:15Z
---
# Remove output as format to target syntax from docs

## Task

Remove all references to `output ... as <format> to <target>` syntax from docs. This syntax was never implemented in the parser and will not be implemented.

## Files to Edit

Primary file with detailed invalid examples:
- `docs/src/atoms/commands/output.md` — lines 15-37 show `output @x to "file"` and `output @x as yaml to "file"`

Also search and update these files:
- `docs/user/reference.md`
- `docs/src/explainers/introduction.md` (line 265)
- `docs/dev/OUTPUT.md`
- `docs/llm/llms-language-reference.txt`
- `docs/user/flow-control.md`

Run `grep -rn "output.*as json\|output.*as yaml\|output.*as csv\|output.*as.*to" docs/` to find all instances.

## What to Replace With

Where examples show `output @data as json to "file.json"`, replace with the transformer pattern: `output @data.json()`. Do not invent new syntax — only use patterns that currently parse and work.

