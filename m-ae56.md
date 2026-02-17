---
id: m-ae56
status: closed
deps: []
created: 2026-02-16T17:07:19Z
type: bug
priority: 0
assignee: Adam
tags: [bug, parser, p0, qa-driven]
updated: 2026-02-16T17:13:02Z
---
# bug: bare bail parser consumes next statement as message

## Problem

When `bail` is followed by another directive like `show`, the parser incorrectly consumes the next statement as bail's message argument.

## Repro

```mlld
bail
show "this should print"
```

Expected: bail terminates with default message, show never reached but parses correctly.
Actual: Parser treats `show "this should print"" as bail's message argument.

## Where to look

- Grammar: `grammar/directives/bail.peggy` — the message argument is likely too greedy
- The bail grammar rule needs to not consume the next line as its argument when bail is bare (no message)
- Test cases: `tests/cases/docs/atoms/control-flow/bail/` or create new ones in `tests/cases/`

## Fix approach

The bail directive's message parameter should be optional and terminate at end-of-line. If `bail` appears alone on a line, it should use the default message ("Script terminated by bail directive.").

## Verification

```bash
npm run ast -- 'bail
show "hello"'
```

Should parse as two separate directives: a bare bail and a show.

## Source
QA run: qa/runs/2026-02-16-0/topics/bail/


**2026-02-16 17:13 UTC:** Fixed bail grammar to require same-line message parsing; added regression in interpreter/eval/bail.test.ts for bare bail followed by show; all tests pass via npm test.
