---
id: mlld-8zi
status: closed
deps: []
links: []
created: 2025-12-09T11:35:40.40568-08:00
type: bug
priority: 1
---
# Grammar: @ in method string arguments incorrectly parsed as variable reference

In when-expressions, method calls with double-quoted string arguments containing @ like `.startsWith("@")` fail to parse because the @ is being interpreted as a variable reference instead of a literal character in the string.

Reproduction:
```
/exe @fn(msg) = when first [
  @msg.body.startsWith("@") => "yes"
  * => "no"
]
```

This should parse successfully but fails with 'Invalid /exe syntax'.

Workaround: Use single quotes instead: `.startsWith('@')`


