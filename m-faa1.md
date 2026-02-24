---
id: m-faa1
status: closed
deps: []
links: []
created: 2026-02-24T10:29:32Z
type: bug
priority: 1
assignee: Adam
tags: [interpreter, file-loading]
updated: 2026-02-24T10:36:29Z
---
# JSON file object iteration bug: for @k, @v iterates wrapper properties

When loading a .json file with <file.json>, direct property access works (@config.syntax.tag returns the expected value). But for @k, @v in @config iterates the file object wrapper properties (type, text, data, metadata, toString, valueOf) instead of the JSON data keys (syntax, commands, etc). This breaks object iteration on loaded JSON files.

