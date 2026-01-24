---
id: mlld-si08.5
status: closed
deps: []
links: []
created: 2026-01-19T22:24:27.111703-08:00
type: task
priority: 1
parent: mlld-si08
---
# influenced label propagation (untrusted-llms-get-influenced)

## Goal

Implement the `influenced` label propagation from spec v4 Part 1.8.

## Concept

When untrusted data is in an LLM's context and the LLM produces output, that output receives the `influenced` label. This tracks that the LLM's decision-making was potentially affected by untrusted input.

## Enabling

```mlld
policy {
  defaults: {
    rules: ["untrusted-llms-get-influenced"]
  }
}
```

## Behavior

```mlld
exe llm @processTask(task) = run cmd { claude -p "@task" }

// If @task contains untrusted data:
// 1. LLM processes the task
// 2. LLM output gets: influenced label
// 3. influenced propagates through subsequent operations
```

## How It Works

1. Track when `llm`-labeled exe receives untrusted input
2. Mark all outputs from that exe as `influenced`
3. Normal taint propagation handles the rest

**Key insight**: No special propagation logic needed beyond normal taint. The rule just auto-labels LLM outputs when untrusted was in context.

## Implementation Tasks

1. Detect `llm`-labeled exe calls
2. Check if any input has `untrusted` taint
3. If yes, add `influenced` to output taint
4. Wire into `defaults.rules` opt-in system

## Exit Criteria

- [ ] `llm`-labeled exe with untrusted input → output gets `influenced`
- [ ] `influenced` propagates normally through transformations
- [ ] Only enabled when `"untrusted-llms-get-influenced"` in rules
- [ ] Works with manually labeled `llm` exes

## References

- Spec v4 Part 1.8: The `influenced` Label
- Spec v4 Part 3.2: The `defaults` Object (rules)

## Estimated Effort

~3 hours



