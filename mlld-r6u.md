---
id: mlld-r6u
status: closed
deps: []
links: []
created: 2025-12-17T08:30:09.750552-08:00
type: feature
priority: 1
---
# Detect JSON in markdown code fences and suggest @json.llm

When @json filter fails to parse, check if the input looks like JSON wrapped in markdown code fences (starts with ```json or ```). If so, suggest using @json.llm filter instead. This is a common pattern when working with LLM responses.


