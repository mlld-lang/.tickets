---
id: m-6bbf
status: closed
deps: []
links: []
created: 2026-03-24T04:40:29Z
type: chore
priority: 1
assignee: Adam
tags: [pipes, templates, evaluation]
updated: 2026-03-24T05:00:42Z
---
# Improve validation for pipes in function args

When passing a pipe expression as an argument to a template exe, the pipe is not evaluated — the literal expression string is passed instead.

```mlld
exe @myPrompt(data) = template "./prompt.att"

exe @worker(input) = [
  let @prompt = @myPrompt(@input | @pretty)
  >> Expected: @pretty transforms @input to formatted JSON, passes result to template
  >> Actual: template receives the literal string "@input | @pretty"
]
```

The LLM inside the template reported seeing `@task | @pretty` as literal text rather than the resolved JSON value.

Workaround: pre-compute the pipe into a let variable before passing to the template:
```mlld
let @formatted = @input | @pretty
let @prompt = @myPrompt(@formatted)
```

However, this workaround was also reported to not fully resolve the issue (needs further investigation).

Note: mlld prettyprints JSON in templates automatically, so `| @pretty` is unnecessary for template arguments. But the pipe-not-evaluating behavior is still a bug — it should work even if it is redundant here.

Discovered in: agentdojo attack-impl orchestrator, where `@implementPrompt(@task | @pretty, @catalogEntry | @pretty, ...)` passed literals to the .att template.


## Notes

**2026-03-24T05:00:42Z** Added grammar-level parse error for pipe expressions inside function arguments. @func(@var | @pipe) now fails with a clear error suggesting the let-variable workaround.
