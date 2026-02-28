---
id: m-c8ab
status: closed
deps: [m-e510]
created: 2026-02-10T19:52:00Z
type: task
priority: P0
assignee: Adam
updated: 2026-02-27T04:51:38Z
---
# Rename @json to @parse

Rename the JSON parsing pipe from @json to @parse. 'parse' describes the action; 'json' describes a format.

- @parse — parse string to structured data
- @parse.llm — extract JSON from messy LLM output (backtick fences, comments)
- @parse.strict / @parse.loose — JSON5 variants
- Future: @parse.yaml, @parse.csv read naturally; @json.yaml would be nonsensical

@parse.llm is the highest-value variant — used constantly in orchestrators.

@parse stays as a standalone global transform — not under @mx.*. Transforms are pipe targets (verbs/actions), not system metadata. @result | @parse reads naturally; @result | @mx.parse adds verbosity for no benefit.

Requires m-e510 (builtin shadowing) first — introducing @parse as a new builtin name would break any existing exe @parse(...) definitions unless builtins are shadowable.

Keep @json as deprecated alias. mlld validate warns on @json usage with suggestion to use @parse (but not if user has defined their own @json variable — that's a valid shadow, not deprecated usage).

Acceptance criteria:
- @parse, @parse.llm, @parse.strict, @parse.loose all work
- @json.* continues working as deprecated alias
- mlld validate warns on @json usage
- Docs and error messages updated to use @parse
- Tests updated
