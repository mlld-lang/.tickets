---
id: mlld-wmzl.4
status: closed
deps: [mlld-wmzl.14]
links: []
created: 2026-01-16T07:18:33.541581-08:00
type: task
priority: 0
parent: mlld-wmzl
---
# Phase 4: Label flow checker (core defense)

## Goal

Enforce label flow rules before any execution. Policy checks run before guards and are non-bypassable.

## Flow Context

```typescript
export interface FlowContext {
  inputTaint: string[];  // labels + taint markers
  opLabels: string[];    // op:* labels for the operation
  exeLabels: string[];   // labels from /exe definitions
  flowChannel?: 'using' | 'arg' | 'stdin';
  command?: string;      // command name for op context
}
```

## Label Flow Check

- Prefix matching (`op:cmd:git` matches `op:cmd:git:status`).
- Most-specific-wins for allow vs deny.
- `flowChannel === 'using'` bypasses label flow checks.
- Default:deny semantics are deferred (see `mlld-wmzl.15`).

## Enforcement

`interpreter/policy/PolicyEnforcer.ts` throws `MlldSecurityError` with context.

Policy enforcement runs before guards at all execution points, including:
- `interpreter/eval/directive.ts` (central dispatch)
- `interpreter/eval/run.ts`
- `interpreter/eval/show.ts`, `output.ts`, `append.ts`, `stream.ts`
- `interpreter/eval/exec-invocation.ts`
- `interpreter/eval/pipeline/builtin-effects.ts`
- `interpreter/eval/pipeline/command-execution.ts`

## Exit Criteria

- [ ] `checkLabelFlow` supports prefix matching and most-specific-wins
- [ ] `flowChannel: 'using'` bypasses
- [ ] PolicyEnforcer throws with details
- [ ] Enforcement runs before guards across all execution paths
- [ ] Tests cover allow/deny and prefix behavior



