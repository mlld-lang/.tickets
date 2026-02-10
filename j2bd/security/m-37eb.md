---
id: m-37eb
status: closed
deps: []
created: 2026-02-09T08:41:30Z
type: bug
priority: 2
assignee: Adam Avenir
updated: 2026-02-09T22:35:40Z
---
# Pipeline effect guards: bare pipeline effects not executed

tests/cases/security/effect-guard-{append,log,output}-pipeline: Bare pipeline expressions like '@data | append file.txt' in text nodes are returned as raw text, never executed. Guard hooks never called. Affects 3 tests.


**2026-02-09 22:35 UTC:** Fixed in interpreter/core/interpreter.ts by detecting markdown Text nodes that contain only bare builtin-effect pipelines (parsed via ForBlockStatementList) and executing those statements instead of emitting raw text. Removed skips for effect-guard-{append,log,output}-pipeline and rebuilt fixtures. Verified with: npx vitest run interpreter/interpreter.fixture.test.ts -t 'effect-guard-(append|log|output)-pipeline' (3 passing).
