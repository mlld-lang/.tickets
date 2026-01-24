---
id: mlld-v1uv
status: closed
deps: []
links: []
created: 2026-01-05T01:13:45.888883-08:00
type: bug
priority: 2
---
# Grammar: ProseInlineContent doesn't parse @var references

ProseInlineContent in grammar/patterns/exe-rhs.peggy:400-409 captures prose content as raw text, doesn't parse @var references.

Inline prose blocks like:
exe @greet(name) = prose:@config { session "Hello @name" }

Don't interpolate @name - it stays literal in output.

Fix: Use TemplateBodyAtt-style parsing. See grammar/base/unified-templates.peggy:291 for pattern.

Test: modules/tmp/test-prose-interp.mld


