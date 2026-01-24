---
id: mlld-3gm
status: closed
deps: []
links: []
created: 2025-12-09T08:38:03.216807-08:00
type: feature
priority: 1
---
# Grammar: template path should support variable interpolation

**Reported by:** partydev during SDK dogfooding

**Current behavior:**
`template "../agents/@agent.att"` treats the path as a literal string - @agent is NOT interpolated.

**Expected behavior:**
Variables in the template path should be interpolated, same as in backticks or angle brackets.

**Current workarounds:**
1. Use a variable for the full path:
```mlld
/var @templatePath = \`../agents/@agent.att\`
/exe @buildPrompt(message) = template @templatePath
```

2. Use angle brackets + inline template:
```mlld
/var @tpl = <../agents/@agent.att>
/exe @buildPrompt(message) = ::@tpl::
```

**Root cause:**
`QuotedStringPath` in `grammar/patterns/path-expression.peggy` uses `$([^"]*)` which captures content as-is without parsing for variable references.

**Fix needed:**
Update `QuotedStringPath` to parse variable references similar to how `AlligatorPath` does, or create a new `InterpolatedQuotedPath` pattern for template paths.


