---
id: mlld-evv
status: closed
deps: []
links: []
created: 2025-12-18T11:54:55.980867-08:00
type: task
priority: 2
---
# Add .mx.key accessor for object values during iteration

Currently users can access keys during iteration via @var_key suffix. But now that we have .mx.keys, .mx.values, .mx.entries on objects, it would be more consistent to also support .mx.key on individual items when iterating.

Example use case:
/for @agent in @agentRegistry => show `Key: @agent.mx.key`

This would be cleaner than @agent_key suffix and more consistent with the new .mx namespace for objects.

Note: .mx.value is probably unnecessary since the item itself IS the value. But .mx.key would be useful for consistency.


