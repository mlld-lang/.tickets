---
id: m-f0d1
status: open
deps: []
created: 2026-02-18T17:16:59Z
type: The mlld plugin skills (orchestrator, agents, scaffold) don't mention @exists() as a built-in. As a result, orchestrator examples and scaffolds use a manual sh-based workaround: exe @fileExists(path) = sh { test -f "$path" && echo "yes" || echo "no" } which returns strings instead of booleans. @exists() already handles file paths, globs, variables, and field access natively and returns proper booleans. Action items: 1. Document @exists() in the orchestrator skill gotchas/syntax-reference 2. Update scaffold templates to use @exists() instead of the sh workaround 3. Update example orchestrators (research, development, audit) that define @fileExists
priority: p0
assignee: Adam Avenir
---
# Make @exists() built-in more discoverable in plugin skills

