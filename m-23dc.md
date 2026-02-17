---
id: m-23dc
status: closed
deps: []
created: 2026-02-16T00:52:18Z
type: bug
priority: 0
assignee: Adam
updated: 2026-02-16T01:09:44Z
---
# Fix gotchas.md factual errors

gotchas.md is the most-consulted troubleshooting doc and contains multiple factual errors:
1. Claims cmd rejects pipes — cmd allows pipes, rejects >, &&, ;
2. Error objects shown as .__error, .__message — actual fields are .error, .message
3. show { result: @parsed } example — parser rejects { } as command block
4. When+show patterns marked WRONG — they work fine
5. Reserved names shown as causing ERROR — they produce info hints
6. Uses @json — @json is deprecated, use @parse
Affects 10+ downstream topics.

