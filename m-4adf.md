---
id: m-4adf
status: open
deps: []
links: []
created: 2026-03-05T22:46:33Z
type: task
priority: 1
assignee: Adam
tags: [nexus, docs, sdk]
---
# [User Request] Python SDK integration guide - using mlld as orchestration layer from Python

The Python SDK README shows the API but not the integration pattern. An external team evaluated mlld and concluded they'd need to either (a) lose their entire Python LLM client stack, or (b) build a custom Python AST evaluator. They missed that the SDK lets Python stay in control while mlld handles orchestration. Need a guide showing: the SDK persistent RPC model (not subprocess-per-call), how Python code calls mlld for orchestration while keeping its own infrastructure, when mlld-as-orchestrator vs mlld-as-helper makes sense, and the tradeoffs honestly. Target audience: Python teams evaluating mlld adoption.

