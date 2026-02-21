---
id: m-7fc3
status: closed
deps: []
created: 2026-02-20T19:55:51Z
type: bug
priority: 0
assignee: Adam
updated: 2026-02-20T20:10:37Z
---
# Template literal interpolation strips security labels from variables

When a pii-labeled variable is interpolated into a template literal and passed as an argument to an exe, the resulting value loses its labels. Direct variable references propagate labels correctly.

Repro:
```mlld
var pii @name = "John Doe"
exe @echo(input) = cmd { printf "@input" }

var @direct = @echo(@name)
show @direct.mx.labels        # ["pii"] ✓

var @template = @echo(`hello @name`)
show @template.mx.labels      # [] ✗ — should be ["pii"]
```

This breaks the security composition story: any orchestrator that builds a prompt via template interpolation before passing to an LLM exe will silently lose all label tracking. Guards that depend on label propagation (e.g., blocking pii from net:w operations) become ineffective.

## Acceptance Criteria

Template literals that interpolate labeled variables carry the union of all interpolated labels. In the repro above, @template.mx.labels should include "pii".


**2026-02-20 20:01 UTC:** Reproduced on 2026-02-20: /var pii @name + /exe @echo(input)=cmd { printf "@input" } => @echo(@name) keeps [pii], but @echo() yields []. Root cause in interpreter/eval/exec-invocation.ts: interpolation descriptors are collected into resultSecurityDescriptor, then overwritten at lines ~1429-1433 by descriptorPieces (which may include empty param descriptors). Proposed fix: merge descriptorPieces INTO existing resultSecurityDescriptor instead of replacing; add regression test for template-literal arg propagation through exe params.

**2026-02-20 20:01 UTC:** Clarification: failing case is var @template = @echo(backtick-quoted template containing @name). direct @echo(@name) keeps labels, templated arg loses them.

**2026-02-20 20:10 UTC:** Fixed in interpreter/eval/exec-invocation.ts: descriptorPieces are now merged into existing resultSecurityDescriptor instead of overwriting it, preserving descriptors collected during template interpolation. Regression test added in tests/interpreter/security-metadata.test.ts: preserves labels for template-literal exe arguments. Verified with vitest targeted runs.
