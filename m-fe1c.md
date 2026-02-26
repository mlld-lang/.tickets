---
id: m-fe1c
status: closed
deps: []
created: 2026-02-24T16:51:01Z
type: bug
priority: 0
assignee: Adam Avenir
tags: [docs]
updated: 2026-02-24T18:27:31Z
---
# Remove aspirational > file redirect syntax from output docs

## What's wrong

Output docs show `output @data as json > file.json` syntax. The grammar has no `>` redirect rule — this produces a parse error. The working syntax is `output @data as json to "file.json"`.

## Fix

Remove all `> file` redirect examples from output docs. Standardize on `to "file"` syntax everywhere.

