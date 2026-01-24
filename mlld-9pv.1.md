---
id: mlld-9pv.1
status: closed
deps: []
links: []
created: 2025-12-10T02:51:04.553133-08:00
type: task
priority: 1
parent: mlld-9pv
---
# Grammar-core: add reparseInner helper

Add helpers.reparseInner(innerText, startRule, offset) in grammar/deps/grammar-core.ts to reparse block contents and rethrow mlldError with corrected locations. Should offset location() by block start and be safe for block contexts only.

## Notes

Reparse helper implemented and wired; tests added.


