---
id: mlld-8ml
status: closed
deps: []
links: []
created: 2025-12-08T19:40:41.102258-08:00
type: bug
priority: 0
---
# Pipeline state machine uses text channel instead of structured values

PipelineExecutor loops state machine transitions on the .text string while stage inputs/outputs are StructuredValue. State machine START/STAGE_RESULT transitions only see text, so metadata/provenance/security are invisible to control flow and retries. Align state machine payloads with structured values (or dual channel) so pipeline retries and downstream guards use full data.


