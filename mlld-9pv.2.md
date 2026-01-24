---
id: mlld-9pv.2
status: closed
deps: []
links: []
created: 2025-12-10T02:51:11.017812-08:00
type: task
priority: 1
parent: mlld-9pv
---
# Wire reparse helper for exe blocks

Use grammar-core reparse helper in exe statement block parsing to re-run inner text on failure and surface inner-line errors; include unterminated block and return-last checks.

## Notes

Exe block reparse wiring done; regression test covers block errors.


