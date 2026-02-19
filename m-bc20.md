---
id: m-bc20
status: open
deps: []
created: 2026-02-19T17:15:06Z
type: feature
priority: 0
assignee: Adam Avenir
---
# Validator: @json deprecation warning should explain both serialization and parsing paths

The @json deprecation warning says 'use @parse instead' but this is confusing because @json was commonly used for serialization (object to JSON string), not parsing. Users don't know: (1) whether @parse handles both directions, (2) whether they need the pipe at all (objects auto-serialize in templates and output directives). The warning should be educational, explaining both replacement paths and the auto-serialization pattern.

## Acceptance Criteria

1. Warning message explains both intents (parsing vs serialization). 2. Warning mentions auto-serialization as the common case where the pipe can be removed entirely. 3. Message includes concrete examples of correct replacements.

