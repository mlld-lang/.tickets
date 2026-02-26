---
id: m-b20c
status: closed
deps: []
created: 2026-02-25T04:12:43Z
type: bug
priority: 0
assignee: Adam Avenir
updated: 2026-02-25T17:42:14Z
---
# Pipeline grammar silently drops stages before builtin effects

## Bug

`var @t = "hello" | @addBang | log` produces an AST with only `[log]`. The `@addBang` stage is silently dropped. This affects any custom pipeline stage (@exe call, js{} block, @transformer) immediately before a builtin effect stage (log, show, output). Custom-to-custom pipelines like `@a | @b` work fine.

## Acceptance criteria

1. `npm run ast -- 'var @t = "hello" | @addBang | log'` — the AST must contain BOTH @addBang AND log as pipeline stages, in that order
2. `npm run ast -- 'var @t = @input | @transform | @validate | show'` — all three stages must appear in the AST
3. `npm run ast -- 'var @t = @a | @b | @c'` — must still work (regression check for custom-to-custom)
4. All existing tests pass: `npm test`

## Where to look

The pipeline grammar rules in `grammar/`. The builtin effect alternatives (log/show/output) are likely matching too eagerly and consuming the preceding custom stage. The fix is in the Peggy grammar, not the interpreter.

## How to verify before and after

```bash
npm run ast -- 'var @t = "hello" | @addBang | log'
```

Before fix: AST pipeline stages array contains only `[log]`
After fix: AST pipeline stages array contains `[@addBang, log]`
