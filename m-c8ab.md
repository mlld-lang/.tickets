---
id: m-c8ab
status: open
deps: [m-a310]
created: 2026-02-10T19:52:00Z
type: task
priority: P0
assignee: Adam
updated: 2026-02-10T19:52:39Z
---
# Rename @json to @parse (under @mx.parse)

Rename the JSON parsing pipe from @json to @parse. 'parse' describes the action; 'json' describes a format.

- @parse (or @mx.parse) — parse string to structured data
- @parse.llm (@mx.parse.llm) — extract JSON from messy LLM output (backtick fences, comments)
- @parse.strict / @parse.loose — JSON5 variants
- Future: @parse.yaml, @parse.csv read naturally; @json.yaml would be nonsensical

@parse.llm is the highest-value variant — used constantly in orchestrators.

Implement as part of the @mx.* namespace migration (m-a310). Keep @json as deprecated alias.

Related: m-a310 (@mx namespace)

