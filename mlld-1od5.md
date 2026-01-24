---
id: mlld-1od5
status: closed
deps: [mlld-yb8a]
links: []
created: 2026-01-14T15:16:32.83823-08:00
type: task
priority: 1
parent: mlld-7suk
---
# Phase 1.3: Document security tracking model

## Summary
Document the three-dimensional security tracking model and MCP security semantics for guard authoring.

## Context
mlld tracks security context across three dimensions:
- `@mx.labels` - Semantic classification (what it IS): secret, pii, untrusted
- `@mx.taint` - Source markers (where it CAME FROM): src:mcp, src:exec, src:file
- `@mx.sources` - Transformation chain (HOW it got here): mcp:createIssue, guard:@sanitize

This is currently undocumented and critical for effective guard authoring.

## Implementation

### Documentation to Create/Update

1. **Add to spec**: `todo/spec-mcp-env-policy.md`
   - Add "Part 1.5: Complete Security Tracking Model" section
   - Document all three dimensions with examples
   - Show when to check labels vs taint vs sources

2. **Update guard docs**: `docs/user/guards.md` or similar
   - Document that guards don't short-circuit (intentional for audit trail)
   - Document @mx.op structure: type, subtype, name, labels, @mx.op.metadata.command
   - Show examples for each dimension

3. **Update MCP docs**: `docs/user/mcp.md`
   - Document MCP name mapping: snake_case exposed to clients, camelCase in @mx.op.name
   - Document function label propagation to @mx.op.labels
   - Document security tracking for MCP calls:
     - labels: ["untrusted"]
     - taint: ["src:mcp"]
     - sources: ["mcp:toolName"]

4. **Add security best practices**
   - Document that environment modules must NOT use `with { guards: false }`
   - Document that LLM is a taint barrier (cannot track through neural network)
   - Document zero-arg tool handling (invocation descriptor ensures tracking)

### Key Points to Document

1. **Guard Behavior**
   - Guards accumulate denies, don't short-circuit
   - Final decision based on accumulated result
   - This is intentional for audit logging

2. **@mx.op Structure**
   - @mx.op.type: "exe", "run", "show", etc.
   - @mx.op.name: function name (camelCase for MCP tools)
   - @mx.op.labels: labels from /exe declaration
   - @mx.op.metadata.command: command string for /run

3. **When to Use Each Dimension**
   - @mx.labels: "Should this data type be allowed here?"
   - @mx.taint: "Did this data come from an untrusted source?"
   - @mx.sources: "What operations touched this data?"

## Exit Criteria
- [ ] spec-mcp-env-policy.md has Part 1.5 section
- [ ] Guard documentation explains three dimensions
- [ ] MCP documentation explains security tracking
- [ ] Examples show each dimension in use
- [ ] Zero-arg tool handling documented
- [ ] LLM as taint barrier explained

## Tests
No code tests - documentation only

## Dependencies
- Depends on: Phase 1.2 (@mx.taint exposure) - need working feature to document

## Estimated Effort
4 hours


