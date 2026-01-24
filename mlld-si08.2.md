---
id: mlld-si08.2
status: closed
deps: [mlld-si08.1]
links: []
created: 2026-01-19T22:23:35.07782-08:00
type: task
priority: 1
parent: mlld-si08
---
# Auto-sign implementation (defaults.autosign)

## Goal

Implement auto-signing from spec v4 Part 14.5.

## Syntax

```mlld
policy {
  defaults: {
    autosign: ["templates"]              // Auto-sign all templates
    // or:
    autosign: ["templates", "variables"] // Sign everything
    // or:
    autosign: {
      templates: true,
      variables: ["@*Prompt", "@*Instructions"]  // Glob patterns
    }
  }
}
```

## Behavior

When `autosign` is enabled:
1. Matching variables are signed on creation
2. Signatures cached based on content hash
3. Re-signed automatically if content changes
4. No manual `sign` calls needed

## Caching

```
First run:     template loaded → hash computed → signed → cached
Subsequent:    template loaded → hash matches → cached signature used
Template edit: template loaded → hash differs → re-sign → cache updated
```

## Implementation Tasks

1. Parse `defaults.autosign` in policy
2. Hook into template loading to auto-sign
3. Hook into variable creation for pattern matching
4. Content hash caching logic
5. Re-sign on content change detection

## Dependencies

- mlld-si08.1 (Signing primitives)

## Exit Criteria

- [ ] `autosign: ["templates"]` auto-signs all templates
- [ ] `autosign: { variables: ["@*Prompt"] }` uses glob patterns
- [ ] Signatures cached by content hash
- [ ] Re-sign on content change works
- [ ] No manual `sign` needed when autosign enabled

## References

- Spec v4 Part 14.5: Auto-Signing

## Estimated Effort

~4 hours



