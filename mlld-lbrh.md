---
id: mlld-lbrh
status: closed
deps: []
links: []
created: 2025-12-20T09:16:36.877973-08:00
type: bug
priority: 2
---
# LSP: 'ge' in @agent wrongly highlighted on first occurrence only

In for loops like `for @agent in @requiredAgents => when first [...]`, the 'ge' substring in the first @agent gets wrong highlighting. Subsequent @agent references in the same block are fine. Likely a tokenization boundary issue in the LSP visitor.


