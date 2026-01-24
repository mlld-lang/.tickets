---
id: mlld-ghp
status: closed
deps: []
links: []
created: 2025-12-12T18:09:19.520324-08:00
type: bug
priority: 0
---
# Negative token position on line 14 causing tokenization failure

LSP error: char: -1, length: 17, tokenType: 'variableRef' on line 14 of orchestrate.mld. This invalid token causes entire semantic token response to fail (0 tokens sent). Blocks all highlighting. Line: /exe @getReplyPressure(agent, msg) = [


