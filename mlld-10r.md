---
id: mlld-10r
status: closed
deps: []
links: []
created: 2025-12-14T07:55:57.593696-08:00
type: bug
priority: 1
---
# Grammar: Support cmd(@arg) parameter syntax

Currently 'run cmd(@arg) { ... }' fails to parse. Grammar error: 'Invalid /run syntax. Expected: /run cmd {command}, /run language {code}, or /run @exec'

This is a GRAMMAR limitation, not a tokenization bug. The grammar needs to be updated to allow parameters for cmd blocks, similar to how js(@arg) and other languages might work.

Requires grammar changes in:
- grammar/src/* (peggy.js rules for /run directive)
- AST updates to support cmd parameters
- Interpreter support for cmd parameters


