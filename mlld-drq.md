---
id: mlld-drq
status: closed
deps: []
links: []
created: 2025-12-10T20:20:32.153268-08:00
type: feature
priority: 2
tags: [size-l, complexity-l, risk-l, impl-done]
updated: 2026-01-30T06:46:53Z
---
# Template collection import: /import templates from dir as @name

**Problem:** Users can't dynamically load templates based on runtime values. The current `/exe @name() = template "path.att"` resolves the path at definition time, so parameters can't be used in paths.

**Syntax:**
```mlld
/import templates from "@base/agents" as @agents(message, context)
/import templates from "@base/formatters" as @formatters(data, format)

>> All templates in agents/ share the (message, context) signature
/show @agents["alice"](@msg, @ctx)
/show @agents["bob"](@msg, @ctx)

>> Nested directories via dot notation
/show @agents.support["helper"](@msg, @ctx)

>> Dynamic in loops
/for @agent in @agentList [
  show @agents[@agent.name](@message, @context)
]
```

**Key design: Explicit parameter contract**
- Parameters declared in the import: `as @agents(message, context)`
- ALL templates in that directory must accept those params
- Different param signatures = different template groups
- No inference needed - contract is explicit

**Validation:**
- Template uses `@message` ✓ (in declared signature)
- Template uses `@foo` ✗ → "Template 'alice.att' references @foo but signature only declares (message, context)"
- Unused params are fine (template doesn't have to use all declared params)

**Directory structure:**
- `agents/alice.att` → `@agents["alice"]`
- `agents/support/helper.att` → `@agents.support["helper"]`
- Directories become dot segments, filenames stay in brackets

**Errors:**
- Template not found: "Template 'unknown' not found in @agents. Available: alice, bob, support/helper"
- Param mismatch at parse time (template references undeclared var)

**Implementation notes (via gpt5.1 analysis):**
- Add `templates` import type to grammar
- Detect `importType === 'templates'` and walk directory instead of blocking
- Build nested moduleObject with `__executable` entries keyed by path
- Parse each template with `parseSync(..., {startRule: 'TemplateBodyAtt'})`
- Validate template AST against declared params at parse time
- Keep single-file `.att` import banned - use `/exe @name() = template "file.att"` for that
- Consider lazy loading (don't parse until first invocation) for performance
- Security: ensure traversal stays under base dir, taint labels track each file

**Security:** Base directory fixed at definition time, only filename/index varies at runtime. Provenance trackable. All templates get taint from their source file.

Reported by @partydev's use case in chat.


