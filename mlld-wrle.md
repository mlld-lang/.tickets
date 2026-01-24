---
id: mlld-wrle
status: closed
deps: []
links: []
created: 2026-01-01T17:06:53.839536-08:00
type: bug
priority: 0
---
# :::...::: template outputs AST JSON instead of string with large content

Triple-colon templates (`:::...:::`) output AST JSON instead of interpolated strings when the interpolated content is large (e.g., joined file contents).

**Reproduction:**
```mlld
var @contents = for @m in ["a.txt", "b.txt"] => <docs/llm/@m>
var @body = @contents.join("\n\n")
var @result = :::<GUIDE>{{body}}</GUIDE>:::
show @result  >> outputs AST JSON, not the expected string
```

**Expected:** `@result` contains the interpolated string with file contents.

**Actual:** `@result` contains JSON AST representation like:
```json
[{"type":"Text","nodeId":"...","content":"<GUIDE>"},{"type":"VariableReference",...}]
```

**Works correctly with:**
- Small strings: `var @x = "test"; var @r = :::{{x}}:::` works
- JS templates: `exe @f(x) = js { return \`<GUIDE>${x}</GUIDE>\` }` works

**Impact:** `:::...:::` escape hatch is unusable for its intended purpose (XML with interpolation).

**Discovered in:** llm/run/llmstxt.mld build script - had to fall back to JS.


