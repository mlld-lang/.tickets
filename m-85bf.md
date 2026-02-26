---
id: m-85bf
status: closed
deps: []
created: 2026-02-24T16:51:00Z
type: bug
priority: 0
assignee: Adam Avenir
tags: [docs]
updated: 2026-02-24T18:27:30Z
---
# Remove aspirational .section() method from file-loading docs

## What's wrong

File-loading docs show a `.section()` method for extracting named sections from loaded files. This method does not exist in the interpreter or grammar. Single-section extraction works via the existing load syntax (`load "file.md" > "Section Name"`).

## Fix

Remove all `.section()` method references from file-loading docs. The existing `> "Section Name"` selector syntax is the correct way to do this.

